# Java泛型
## 一、定义
Java泛型是JDK5中引入的新特性，提供在编译时类型安全监测机制，本质是参数化类型（即所操作的数据类型被指定为一个参数）
```
    ArrayList list = new ArrayList();
    list.add("string");
    list.add(true);
    list.add(123);
    
    for(int index = 0 ; index < list.size() ; index++){
	    String str = list.get(index);
	    Log.i("tzy","str = " + str);
    }
```
在没有泛型约束的情况下，写代码的时候`ArrayList`可以添加任何对象（true和123虽然是基础类型，但是有对应的包装类，可以进行自动打包和解包），在for循环中的第一句代码就会报`ClassCastException`异常了，因为集合中第二个对象是`Boolean`对象，而且这个异常在运行的时候才会暴露出来，看一段改进的代码
```
    ArrayList<String> list = new ArrayList<>();
    list.add("string");
    //以下注释的代码在编写代码就会报错提示
    //list.add(true);
    //list.add(123);
    
    for(int index = 0 ; index < list.size() ; index++){
	    String str = list.get(index);
	    Log.i("tzy","str = " + str);
    }
```
添加泛型以后的代码，无法在编译时的状态向集合中添加不符合泛型类型的对象，上面一段简单代码就可以看出泛型意义了

> 泛型只能作用于编译时，对反射（运行时）是无效的。
> 泛型只支持`Object`的类型，而对于8大基础数据类型（`byte`，`short`，`int`，`long`，`float`，`double`，`char`，`boolean`）不支持，所以相应的有8个封装类（`Byte`，`Short`，`Integer`，`Long`，`Float`，`Double`，`Character`，`Boolean`）提供给配合泛型使用，在反省中使用基础类型的时候，封装类有自动装箱/拆箱的功能，为我们省去了麻烦。

## 二、泛型使用
### 1、自定义泛型方法
可以写一个方法，调用该方法的时候可以传入不同类型的参数
> 规则：
> - 声明泛型方法的类型参数声明部分，在方法返回类型之前
> - 每个类型参数和僧名部分包含一个或者多个类型参数，参数中间用逗号隔开
> - 类型参数可以用来声明返回值类型，并且作为泛型方法得到的实际参数类型的占位符

```
public <E> void printArray(E[] array){
	for(E elment:array){
		Log.i（"tzy","elment = " + elment）;
	}
}

public void test(){
	Integer[] intArray = [1,2,3,4];
	Character[] charArray = ['a','b','c','d'];
	String[] stringArray = ["x","y","z","."];
	
	printArray(intArray);
	printArray(charArray);
	printArray(stringArray);
}
```

### 2、自定义泛型类
泛型类的声明和非泛型类的声明类似，在类名后面添加了类型参数声明部分（数量和写法也是一样的）。一个泛型参数（又称类型变量），适用于
```
class Computer｛
	public String toString(){
		return getClass().getSimpleName();
	}
｝

class MacBookPro extends Computer{}
class Alienware extends Computer{}
class Thinkpad extends Computer{}

/**使用泛型的类*/
public class Persion<? extends Computer>{
	private Computer mComputer;
	
	public void setComputer(Computer computer){
		this.mComputer = computer;
	}

	public Computer getComputer(){
		return this.mComputer;
	}
}
```

```
public void test(){
	Persion andy = new Persion<MacBookPro>();
	Persion peter = new Persion<Alienware>();

	andy.setComputer(new MacBookPro());
	peter.setComputer(new Alienware());

	Log.i("tzy","andy's computer is " + andy.getComputer().toString());
	Log.i("tzy","peter's computer is" + peter.getComputer().toString());
}
```

### 3、自定义泛型类接口
泛型接口与泛型类非常相似，只不过定义为`interface`
```
public interface Computer<T>{
	public T getCPU();
}

/**定义实现泛型接口的类*/
class MacBookPro<String> implements Computer<String>{
	private String cpu;
	
	public MacBookPro(T cpu){
		this.cpu = cpu;
	}

	@Override
	public String getCPU(){
		return cpu;
	}
}
```

## 三、类型通配符
类型通配符一般是使用`?`代替具体的类型参数。
例如 `List<?>` 在逻辑上是`List<String>`,`List<Integer>` 等所有`List<具体类型实参>`的父类。

```
public void printData(List<?> data){
	Log.i("tzy","data = " + data.toString());
}

//类型通配符上限通过形如List来定义，如此定义就是通配符泛型值接受Number及其下层子类类型。
public void printData(List<? extends Computer> data){
	Log.i("tzy","data = " + data.toString());
}

//类型通配符下限通过形如 List<? super Number>来定义，表示类型只能接受Number及其三层父类类型
public void printData(List<? spuer Computer> data){
	Log.i("tzy","data = " + data.toString());
}
```

> <? extends T>表示该通配符所代表的类型是T类型的子类。
> <? super T>表示该通配符所代表的类型是T类型的父类，只能接受T及其三层父类类型

##四、类型限界
类型限界再见括号内置顶，它指定参数类型必须具有的性质。不太明白接着往下看，假如现在我想要编写一个findBest的方法，代码如下
```
public interface Comparable<T>{
	boolean compareTo（T other);//接口的中方法自动属于public方法
}

public <Computer> Computer findBest(Computer[] arr){
	int bestIndex = 0;
	
	for(int index = 0 ; index < arr.length ; index ++){
		if(arr[i].compareTo(bestIndex))
			bestIndex = i;
	}

	return arr[bestIndex];
}
```

我们刚开始是这么想的，再来看一遍代码，只有在`Computer`保证有`compareTo（）`情况下才能不抛异常，改变一下代码`public <Computer extends Comparable> Computer`，这样程序不会有问题了，但是更好的写法`public <Computer extends Comparable<Computer >> Computer`，现在就是最优的方案了么？
假设`Computer`实现`Comparable<Computer>`，设`Alineware`继承`Computer`。此时我们可以知道`Alineware`实现`Comparable<Computer>`，这说明`Alineware`符合`Comparable<Computer>`的类型要求，`Computer`是`Alineware`父类，由此可见，我们不需要知道准确的知道`Comparable<T>`中的`T`是什么，因此可以使用通配符，最后的结果就变成了`public <Computer extends Comparable<? extends Computer>>`

```
public <Computer extends Comparable<? extends Computer>> Computer findBest(Computer[] arr){
	int bestIndex = 0;
	
	for(int index = 0 ; index < arr.length ; index ++){
		if(arr[i].compareTo(bestIndex))
			bestIndex = i;
	}

	return arr[bestIndex];
}
```

## 五、类型擦除
由于虚拟机中没有泛型，只有普通类和普通方法，所有泛型类的类型参数在编译时都会被擦除，即将所有的泛型参数用最顶级的父类替换并移除所有类型参数。（这也是反射可以绕过泛型的原因）
>带来的问题：
>	- 无法使用带有泛型的形参来实现多态
>	- 无法catch不同泛型的同一个异常类
>	- 带有泛型的静太类变量是共享的

## 六、注意
 - **static修饰的方法或者域，是不可以引用类的类型变量，因为在类型擦除后类型变量就不存在了。另外，由于实际上只存在一个原始的类，因此static域在该类的泛型实例之间都是共享的**
 - **泛型对象无法直接创建，`new T();`是非法的。`T`由它的限界代替，这可能是`Object`（或甚至是抽象类），所以对`new`的调用没有意义**
 - **泛型数组对象也是不能创建的，由于类型擦除，对T[]的类型转换将无法进行**










    