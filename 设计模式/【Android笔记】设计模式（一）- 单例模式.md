![](https://upload-images.jianshu.io/upload_images/595349-6dd228cf6cee0592.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 本文内容基于《Android源码设计模式解析与实践》

## 1.简述

单例模式是应用最广泛的模式之一，定义就是**单利对象的类必须保证只有一个实例存在**。单例模式适用于创建一个对象需要消耗过多资源的情况，例如访问和数据库等资源是需要考虑使用。

实现单利模式的关键点如下:

* 构造函数私有化（才不会让你有机会在创建一个对象）
* 通过一个静态方法或枚举（后面会有举例）返回单例类对象
* 确保单例类的对象有且只有一个，尤其是多线程环境下（同时是难点）
* 确保单例类的对象在反序列化是不会重新构建对象

## 2.实现

### 饿汉式

```
public class Singleton {
	private final static Singleton instance = new Singleton();
	//私有化构造器
	private Singleton(){}
	
	//共有静态方法，对外暴露获取单例对象
	public static Singleton getInstance(){
		return instance;
	}
}
```

可以看到饿汉式是在声明静态对象时就已经初始化了，如果没有使用单例对象的情况下，就会造成不必要的内存开销。

### 懒汉式

懒汉式是声明一个静态对象，在第一次调用`getInstance()`时进行初始化

```
public class Singleton {
	private static Singleton instance = null;

	private Singleton(){}

	public static synchronized Singleton getInstance(){
		if(null == instance){
			instance = new Singleton();
		}
		return instance;
	}
}
```

与饿汉式不同的地方不仅仅是单例对象初始化的时机，会发现`getInstance()`方法前添加了`synchronized`关键字，也就是`getInstance()`是一个同步方法，以此来保证多线程情况下单例对象的唯一。

相对的，每次调用`getInstance()`都会进行同步，就会消耗不必要的资源，也是懒汉式存在的最大问题。

### Double Check Lock（DCL）

DCL方式实现单力模式的优点在于既能在需要时才初始化对象，又能保证线程安全，而且在对象初始化之后调用`getInstance()`不进行同步

```
public class Singleton {
	private volatile static Singleton instance = null;

	private Singleton(){}

	public static Singleton getInstance(){
		if(null == instance){
			synchronized(Singleton.class){
				if(null == instance){
					instance = new Singleton();
				}
			}
		}
		return instance;
	}
}
```

可以看到`getInstance()`方法中对instance进行了两次判空，第一次判断是为了判断不必要的同步，第二次判断是为了在null的情况下穿件实例；同时instance对象前面还添加了`volatile`关键字，如果不使用`volatile`关键字的话无法保证instance的原子性，这里涉及到`instance = new Singleton();`语句不是一个原子操作。

这句代码最终会被编译成多条汇编指令，大致醉了3件事：

1. 给Singleton的实力分配内存
2. 调用`Singleton()`的构造函数，初始化成员字段
3. 将`instance`对象指向分配的内存空间（此时`instance`就不是null了）

由于Java编译器允许处理器乱序执行，以及JDK1.5之前JMM（Java Memory Model，即java内存模型）中得Cache、寄存器到主内存回写顺序的规定，第二和第三的顺序是无法保证。也就是说执行顺序可能是1-2-3或者是1-3-2。如果是后者，并在3执行完成、2为执行前，备切换到线程B，这时候`instance`已经在线程A中执行过了3，`instance`已经是非空了，所以线程B直接取走`instance`使用时会出错，导致DCL模式失效，而且这种情况难以重现的错误很可能会隐藏很久。

在JDK1.5之后，调整了JVM，具体化了`volatile`关键字。所以在JDK1，5之后的版本在`instance`前添加`volatile`关键字保证每次都是从主内存中读取ujiukeyi使用DCL模式来完成代理模式了。当然，`volatile`或多或少会影响到性能，考虑到正确性这点性能的牺牲还是值得的。

> DCL模式能够在绝大多数场景下保证单例对象的唯一性，资源利用率高，只有第一次加载时反应稍慢，一般能够满足需求

### 静态内部类

在某些情况下DCL模式会出现时效的问题，于是边有了静态内部类的实现方式

```
public class Singleton {
	private Singleton(){}
	public static Singleton getInstance(){
		return SingletonHolder.instance;
	}
	
	/**静态内部类*/
	private static class SingletonHolder{
		public static final Singleton instance = new Singleton();
	}
}
```

当第一次加载`Singleton`类时并不会初始化`instance`，只有在第一次调用`getInstance()`方法是回初始化。第一次调用`getInstance()`方法导致虚拟机加载`SingletonHolder`类，这种方式嫩确保线程安全，也能确保单例对象的唯一性，同时也延迟了单例对象的实例化，所以推荐使用这种实现方式。

### 枚举单例

```
public enmu SingletonEnum {
	INSTANCE;
}
```

就是这么简单粗暴，其实最大优点在于关键点的第4点，即是反序列化也不会重新生成新的实例。

通过序列化可以将单例对象写到磁盘，然后在读取出来，，即使构造函数是私有的，反序列化时依然可以通过特殊的方式创建一个新的实例。反序列化操作提供了一个很特别的钩子函数，类中具有一个私有的、被实例化的方法`readResolve()`，这个方法可以让开发人员控制对象的反序列化。上述几个实例中如果要避免反序列化是重新生成对象，必须加入如下方法：

```
private Object readResolve() throws ObjectStreamException{
	return instance;
}
```

### 使用容器实现单例模式

```
public class SingletonManager{
	private static Map<String,Object> objMap = new HashMap<>();
	
	private SingletonManager(){}
	
	public static void registerService(String key,Object instance){
		if(!objMap.containsKey(key)){
			objMap.put(key,instance);
		}
	}
	
	public static Object getService(String key){
		return objMap.get(key);
	}
}
```

这种方式使得我们可以管理多种类型的单例，并且在使用时可以统一的接口进行操作，降低了使用成本，同时隐藏了具体实现降低了耦合。

> 其实Android中LayoutInflater就是以这种方式实现的。

## 3.总结

不管哪种形式实现单例模式，核心原理都是那四个关键点，具体选择哪种实现方式取决于项目本身以及具体的开发环境等等。

而对于客户端来说通常没有高并发的情况，推荐使用DCL模式或者是静态内部类的方式实现。

#### 优点

* 只存在一个实例，减少了内存开支，减少了系统的性能开销
* 避免对资源的多重占用
* 全局的访问点，优化和共享资源访问

#### 缺点

* 没有接口，难扩展，只能修改代码
* 如果持有Context容易导致内存泄露（需要传递Context的话最好是Application Context）







