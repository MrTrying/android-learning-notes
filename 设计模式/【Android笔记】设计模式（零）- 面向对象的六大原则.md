##1.单一职责原则（Single Responsibility Principle，缩写SRP）
	
单一职责原则，就一个类而言，应该只有一个引起它变化的原因。简单说，一个类应该是一组高度相关的函数、数据的封装；也就是高内聚。

下面代码为 `ImageLoader`（图片加载）类的代码

```
public class ImageLoader{
	//图片缓存
	LruCache<String,Bitmap> mImageCache;
	//线程池，线程数量为CPU的数量
	ExecutorService mExecutorService = Executors.newFixedThreadPool(Runtime.getRuntime().availableProessors());
	
	public ImageLoader(){
		initImageCache();
	}
	
	private void initImageCache() {
   		//省略...         
   }
    
   //显示图片
   public  void displayImage(final String url, final ImageView imageView) {
   		//省略... 
   }
    
   //下载图片
   public  Bitmap downloadImage(String imageUrl) {
        //省略... 
        return bitmap;
   }
}
```

这里可以看出来 `ImageLoader` 类作用有初始化图片缓存、显示图片、下载图片，显然显示图片和下载图片两个方法与初始化图片缓存方法相比作用就显得有些不相关。也就是不符合单一职责原则。按照逻辑进行分拆之后得到`ImageLoader`和`ImageCache`两个类。`ImageLoader`负责图片加载逻辑，`ImageCache`负责处理图片缓存逻辑，这样职责就清楚了，当与缓存相关的逻辑需要改变时，不需要修改ImageLoader类，而图片加载的逻辑需要修改时也不会影响到缓存处理逻辑。

![image](http://upload-images.jianshu.io/upload_images/595349-abe5de26e192247d?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`ImageLoader`代码修改如下##所示：

```
/** 图片加载类 */
public  class ImageLoader {
    //图片缓存
    ImageCache mImageCache = new ImageCache() ;
    //线程池,线程数量为CPU的数量
    ExecutorService mExecutorService = Executors.newFixedThreadPool (Runtime.getRuntime().availableProcessors());

    //加载图片
    public  void displayImage(final String url, final ImageView imageView) {
        //省略... 
     }

    public  Bitmap downloadImage(String imageUrl) {
        //省略... 
        return bitmap;
    }
} 
```
而添加的`ImageCache`类用于处理图片缓存，具体代码如下：
```
public class ImageCache {
    // 图片LRU缓存
    LruCache<String, Bitmap> mImageCache;

    public ImageCache() {
        initImageCache();
    }

    private void initImageCache() {
        //省略... 
    }

    public void put(String url, Bitmap bitmap) {
        mImageCache.put(url, bitmap) ;
    }

    public Bitmap get(String url) {
        return mImageCache.get(url) ;
    }
}
```
如何划分一个类、一个函数的职责，每个人都有自己的看法，这需要根据个人经验、具体的业务逻辑而定。

##2.开闭原则（Open Close Principle，缩写OCP）
开闭原则是Java中最基础的设计原则，知道我们如何建立一个稳定的、灵活的系统。定义：软件中得对象应该对于扩展是开放的，但是对于修改是封闭的。

![](https://upload-images.jianshu.io/upload_images/595349-f88066af78b82bbd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

例如图中`MemonyCache`、`DiskCache`、`DoubleCache`都实现了`ImageCache`接口，`ImageLoader`使用`ImageCache`处理缓存，就意味着`ImageLoader`可以通过`setImageCache()`指定使用哪一种缓存类型，可以使三种缓存其中任意一种，同时不需要修改`ImageLoader`中的代码。这也就是开闭原则的体现。

简单地说，当软件需要变化是，应该尽量通过扩展的方式来实现变化，而不是通过修改已有的代码来实现。“应该尽量”4个字说明OCP原则并不是说绝对不可以修改原始类的，当代码需要需要重构的时候要及时重构，使代码恢复正常，而不是通过继承等方式添加新的实现，这会导致类型的膨胀以及历史遗留代码的冗余。

开发过程中都没有name理想的状况，因此，凡事也是需要结合具体情况在做决定，目的是更稳定、更灵活同时保有原有的正确性。

##3.里氏替换原则（Liskov Substitution Principle，缩写LSP）
里氏替换原则，书上原话的定义简直看不得（解释的辣眼睛，完全看不懂），简单地说就是所有引用基类的地方必须能透明地使用其子类的对象。只要父类能出现的地方子类就可以出现，而且替换为子类也不会产生任何错误或异常，使用者可能根本就不需要知道是父类还是子类。但是，反过来就不行了，有子类出现的地方，父类未必就能适应。其实就是：抽象。 

![](https://upload-images.jianshu.io/upload_images/595349-b4ac20004a407717.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图可以看出，`Window`依赖于`View`，而`Button`和`TextView`继承`View`。这里任何继承自`View`类的子类都可以设置给`show()`方法，也就是**里氏替换原则**。通过里氏替换，就可以自定义各式各样的View，然后传递给Window，并且将View显示到屏幕上。

里氏替换原则的核心原理是抽象，抽象又依赖于继承这个特性，继承的优缺点都相当明

优点：

* 代码重用，减少创建类的成本，每个子类都拥有父类的方法和属性
* 子类与父类基本相似，但又与父类有所区别
* 提高代码的可扩展性

缺点：

*  继承是侵入性的，只要继承就必须拥有父类的所有属性和方法
* 可能造成子类代码冗余、灵活性降低，因为子类必须拥有父类的属性和方法

事物总是具有两面性，如何权衡利与弊都是需要根据具体场景来做出选择并加以处理。

##4.依赖倒置原则（Dependence Inversion Principle，缩写DIP）

依赖倒置原则，说的就是一种特定的就形式，使得高层次的模块不依赖于低层次的模块的实现细节的目的，依赖模块被颠倒了。依赖倒置原则的几个关键点：

* 高层模块不应该依赖低层模块，两者都应该依赖其抽象
* 抽象不应该依赖细节
* 细节应该依赖抽象

是不是觉得和说一个样，至少我是这么觉得的；继续往后看才明白，所谓高层模块就是调用端，低层模块就是具体实现类。依赖倒置原则在 Java 语言中的表现就是：模块间的依赖通过抽象发生，实现类之间不发生直接的依赖关系，通过**接口**或**抽象类**产生依赖关系。什么是依赖关系呢？其实就是相互之间的调用关系。概括来说就是面向接口变成，或者是面向抽象编程。

其实依赖倒置原则主要目的就是解耦

![](https://upload-images.jianshu.io/upload_images/595349-f88066af78b82bbd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

依然可以使用这张图来表示，表达出来就是`ImageLoader`和`MemonyCache`等并没有直接关系，甚至`ImageLoader`只需要实现`ImageCache`类或继承其他已有的`ImageCache`子类完成相应的缓存功能，然后将具体的实现注入到`ImageLoader`即可实现缓存功能的替换。这也是依赖倒置原则的体现。

##5.接口隔离原则（Interface Segregation Principle，缩写ISP）

接口隔离原则将非常庞大、臃肿的接口拆分成为更小的和更具体的接口；目的就是解耦。这个原则的做法和单一职责原则有点相似，就是说接口中得方法保持更高的相关性、尽量少，避免掉不需要的方法。

举个栗子，现在有一个带有呼吸方法的接口，还有一个打鼾方法的接口；如果说，你把这两个方法放大哦一个接口中，基本就是违背接口隔离原则，毕竟呼吸和打鼾没有什么紧密的相关性；不可能说我需要呼吸的时候一定需要打鼾吧！

##6.迪米特原则（Law of Principle，缩写LOP）

迪米特原则也称为最少知识原则（Least Knowledge Principle），定义：一个对象应该对其他对象有最少的了解。通俗地讲，一个类要对自己需要调用的类知道得最少，类的内部如何实现、如何复杂都与调用者（或依赖者）没关系，调用者（或依赖者）只需要知道他需要的方法即可，其他的不需要关心。类与类之间的关系越密切，耦合度越大；当一个类发生改变时，对另一个类的影响也越大。

迪米特法则还有一个英文解释是：Only talk to your immedate friends，翻译过来就是：只与直接的朋友通信。什么叫做直接的朋友呢？每个对象都必然会与其他对象有耦合关系，两个对象之间的耦合就成为朋友关系，这种关系的类型有很多，例如组合、聚合、依赖等。

下图是Volley框架中的`DiskBasedCache`类（实现`Cache`接口）和`Cache`接口的代码

![](https://upload-images.jianshu.io/upload_images/595349-4374dde8aee3b9e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/595349-d57b4afcc201010b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Volley中的Response缓存接口的设计。Cache接口定义了缓存类需要实现的最小接口，依赖缓存类的对象只需要知道这些接口即可。例如缓存的具体实现类DiskBasedCache，该缓存类将Response序列化到本地,这就需要操作File以及相关的类。

Volley的直接朋友就是DiskBasedCache，间接朋友就是mRootDirectory、mEntries等。Volley只需要直接和Cache类交互即可，并不需要知道File、mEntries等对象的存在。


> 文中有引用书本中得例子，也有根据自己理解举的例子，如有不对还望指出。



















