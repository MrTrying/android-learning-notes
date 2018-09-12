![](https://upload-images.jianshu.io/upload_images/595349-6dd228cf6cee0592.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##1.简述

顾名思义，工厂就是生产产品的嘛！工厂模式（Factory Pattern）的定义也差不多就是这个意思，提供了一种创建对象的最佳方式，属于创建型模式。在工厂模式中，创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

将上述的情况对应到code中，工厂模式需要要做的就是帮助我们构建对象，因为构建对象的过程可能比较复杂，我们无法掌握（例如：我们无法直接new出来）。这是对工厂模式的一个大致描述，接下来从各种实现方式说明。

##2.实现方式

###简单工厂模式

首先，简单工厂模式并没有被归纳到23中GOF设计模式中，其实可以理解为工厂模式的简单使用。一个工厂对象决定创建出哪一种类产品，而产品有不同的系列相互之间有些许差异。举个生产鞋的例子，先看UML图

![](https://upload-images.jianshu.io/upload_images/595349-89de8a5b9e85db8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* Factory：工厂类，生产Shoe
* Shoe：抽象的产品
* SportShoe：具体的产品，运动鞋

大致可以分为三部分：工厂类、抽象产品类以及具体产品类；明确结构之后那代码就很简单了，这里忽略对象的创建过程以及鞋子相关对象的代码。

```
/** 鞋的抽象类 */
public abstract class Shoe{

}

/** 运动鞋 */
public class AdidasShoe extends Shoe{

}

/** 工厂类 */
public class ShoeFactory {
    
    public Shoe produceShoe(){
        return new SportShoe();
    }
}
```

这就是最简单的工厂，但是一个只生产运动鞋的工厂，老板想赚更多的钱，要求添加生产`HighHeeledShoe`（高跟鞋）。我们需要对`ShoeFactory`的设计进行修改，如图

![](https://upload-images.jianshu.io/upload_images/595349-2507ad152c6f560a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在`produceShoe()`的函数中添加了`type`参数，表示`Shoe`的不同类型，以此来生产不同的鞋子。我们开始改造`ShoeFactory`类

```
/** 高跟鞋 */
public class HighHeeledShoe extends Shoe{

}

/** 修改后的工厂类 */
public class ShoeFactory {
    
    public Shoe produceShoe(String type){
        switch (type) {
            case "sport":
                return new SportShoe();
            case "highHeeled":
                return new HighHeeledShoe();
            default:
                return new Shoe();
        }
    }
}
```

> case中得字符串可以使用枚举来代替

这样我们可以根绝给定的不同类型生产对应鞋子了。这里看到代码可能会有两个问题：第一，每添加一个品牌就需要怎么加一个case（虽然在实际中是很正常的逻辑，但是在代码层面这种维护方式不那么优雅），同时，简单工厂模式违背了**开闭原则**，即对扩展开放，对修改关闭；因为增加具体产品，就需要修改工厂类代码；第二，调用`produceShoe()`函数时，我们还需要去创建一个`Factory`对象并进行控制管理。

> 直接new出`Factory`的对象，我们就必须自己控制工厂类的构造和生成，同时我们也需要非常清楚工厂的构造函数（比如构造函数有多少个参数，输入参数时有什么条件等等），还需要知道工厂的内部细节，一旦工厂扩展或者改变了，就很可能不知道怎么调用了，对于调用者来说无疑会是噩梦

我们先优化第一个问题，从技术角度我们如何不使用`switch case`这种形式来实现不同类型对象的创建呢？利用**反射**我们就可以实现优化。

对于第二个问题，最直接的解决方案，我们可以直接调用生产鞋子的方法，直接告诉工厂再由工厂生产，我们不需要创建工厂。那就给`produceShoe()`函数添加`static`关键字就可以解决问题了。

```
/** 优化之后的工厂类 */
public class ShoeFactory {
    /**
     * 根据类型生产鞋子
     * @param cls
     * @return
     */
    @Nullable
    public static Shoe produceShoe(Class<? extends Shoe> cls){
        Shoe shoe = null;
        try {
            shoe = (Shoe) Class.forName(cls.getName()).newInstance();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        return shoe;
    }
}
```

这样一来是不是问题就解决了，用静态方则完全不关心是如何构造对象的，我们需要关心工厂的构造细节，即使工厂内部发生变化也不需要关心，**简单工厂（也称为静态工厂）**到这里就结束了，是不是很简单呢？简单工厂模式主要适用于创建抽象子类的业务相同但具体实现不同的对象的情况。

这种写法看似简洁漂亮，但还是有缺点的，使用反射会导致效率降低，当然结合单例模式一同使用也是可以的。

###工厂方法模式

实际上，鞋子的制造商不可能只有一家，肯定存在多家制造商竞争的关系，所以有更多地`Factory`能制造不同鞋子的工厂，`produceShoe`函数就能抽象出来，不同的`Factory`子类根据各自的需要去实现。例如下图中的`NikeFactory`和`DaphneFactory`类

![](https://upload-images.jianshu.io/upload_images/595349-9c7acd773c3c0322.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

相对于简单工厂模式，区别就在于工厂类分为抽象工厂类和具体实现工厂类

```
/** 抽象工厂类 */
pulibc abstract class Factory{
	public abstract Shoe produceShoe(String type)；
}

/** Nike工厂类 */
public class NikeFactory extends Factory {
    
    @Override
    public Shoe produceShoe(String type){
        switch (type) {
            case "sport":
                return new SportShoe();
            case "highHeeled":
                return new HighHeeledShoe();
            default:
                return new Shoe();
        }
    }
}

/** Daphne工厂类 */
public class DaphneFactory extends Factory{
    
    @Override
    public Shoe produceShoe(String type){
        switch (type) {
            case "sport":
                return new SportShoe();
            case "highHeeled":
                return new HighHeeledShoe();
            default:
                return new Shoe();
        }
    }
}
```

使用的时候就简单了，只需要调用对应的工厂，选择客户需要的类型的就可以获得相应的产品了，这样不同的制造商就能分别生产不同的鞋子了。

```
Factory daphneFactory = new DaphneFactory();
adidasFactory.produceShoe("highHeeled");

Factory nikeFactory = new NikeFactory();
nikeFactory.produceShoe("sport");
```

其实有参考挺多例子的，有些举例在产品的抽象类中添加了一些方法，例如：描述函数`desc()`、鞋子的裁剪函数`cut()`等等，个人认为对于一个产品来说，与产品相关的基本都是产品的属性，而对于鞋子这个例子来说，自己主动的有生产的函数不太合适，所以在写Demo的时候并没有添加任何方法。

在工厂的环节采用抽象类的形式实现，其实也可以将`produceShoe`抽象成`IProduceShoe`接口，当然，这也是我的个人理解。其实觉得抽象类或者接口使用其中一个就可以了，不必要同时使用；但也不是绝对的，这个需要根据具体的情况而定。

###抽象工厂模式

生产鞋子的工厂，其实还有可能生产衣服`Cloth`，我们需要就试着为工厂添加制造衣服的这条生产线。为工厂添加生产衣服的生产线之后UML图如下：

![](https://upload-images.jianshu.io/upload_images/595349-243c57b8f7eb0604.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将`ShoeFactory`和`ClothFactory`抽象成接口(还有一个原因Java无法多继承，所以使用interface)，需要的工厂就可以实现相应的接口，来生产对应的产品。可以看出来工厂新增加了`produceCloth`方法，来生产衣服。具体实现其实和之前的实现方式其实是一样的，最大的不同在于添加一个新的生产的产品时，我们需要添加一个对应的功能接口（或者在抽象的Factory类中添加一个新的生产的产品的函数）。

来看下`NikeFactory`的具体代码，只是简单地多实现了一个produceCloth，这样也就实现了`NikeFactory`可以生产多种产品。

```
public class NikeFactory extends Factory {
    @Override
    public Shoe produceShoe(String type){
        switch (type) {
            case "sport":
                return new SportShoe();
            case "highHeeled":
                return new HighHeeledShoe();
            default:
                return new Shoe();
        }
    }


	//新增方法
    @Override
    public Cloth produceCloth() {
        switch (type) {
            case "sport":
                return new SportCloth();
            case "skirt":
                return new Skirt();
            default:
                return new Shoe();
        }
    }
}
```

以生产不同的产品的方式来区分工厂的方法，同时抽象出生产不同产品的这个行为，例如：`produceShoe`和`produceCloth`，让工厂能灵活的生产更多不同的产品。当你需要创建一些对象家族是，抽象工厂也是不错的选择，因为它可以一次性创建多个对象。

抽象工厂非常强大灵活（至少比前两种都要强），可以用多个抽象子类完成复杂的需求，同时保证外界接触不到任何类型的具体的产品类型，某种程度上来说很符合开放封闭原则。当然，缺点也显而易见，模式比较庞大，从UML图就能看出来。

##3.总结

简单工厂暂且不说，工厂方法模式只能生产一个系列的产品；抽象工厂模式通过实现不同的抽象方法可以生产出多个系列的产品。其实以上的各种工厂模式只是为了实现各种情况下，对于调用者代码和具体实现类之间的解耦。

> 说实话，在参考完多篇工厂模式的文章以后，心中对于工厂模式的各种模式有了一定的了解，根据自己的理解完成的本文。但是，在文章结尾时越是看的多，越是觉得每篇文章对于工厂模式整体的描述都不一样，也无法判断对与错。
> 
> SO，对于设计模式，了解目的以及可以使用的场景；使用中多思考，结合自己的实际情况适用才是上策。并不是一成不变的生搬硬套，或许适当的修改会让你的代码更加优雅。
























