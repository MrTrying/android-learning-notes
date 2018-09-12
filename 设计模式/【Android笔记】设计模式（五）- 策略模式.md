![](https://upload-images.jianshu.io/upload_images/595349-6dd228cf6cee0592.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##1.简述

在之前的工厂模式中，为了创建不同的产品使用了`switch case`（或`if else`）的形式实现代码，这样违背了**开闭原则，即对扩展开放、对修改封闭**，维护的成本会随着`cese`（或`else`）的增加而增加，而本文的**策略模式**能较好地解决这个问题。

定义：定义一系列的算法，把它们一个个封装起来, 并且使它们可相互替换。策略模式让算法独立于它的使用者之外，可以自由修改。

![策略模式.png](https://upload-images.jianshu.io/upload_images/595349-286452a59a854726.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

来看看UML图，图中主要由三个部分组成

* Context：上下文环境，持有`Strategy`引用
* Strategy：抽象策略，接口（或抽象类）
* ConcreteStrategy：具体实现策略，实现了具体的算法

> `Strategy`是使用接口还是抽象类，这个取决于一系列的策略中是否有共同属性或方法；如果没有，使用接口更加灵活方便，反之使用抽象类，抽象类中便可存放公共的属性以及方法。

##2.简单实现

我们可以来一场说走就走的旅行，背上行囊，出发去丽江。这个时候我们就需要考虑出行方式和价格的问题了（贫穷）。我们可能需要知道飞机、火车、自驾的花费都是多少，再决定什么方式出行。我们先看看最简单的实现方式

###原始代码

```
public class LetGo {
    public static final String MODE_AIRPLAN = "airPlan";
    public static final String MODE_TRAVEL = "travel";
    public static final String MODE_CAR = "car";

    public void printSpend() {
        Log.i(TAG, "出行花费: ￥" + getSpend(MODE_AIRPLAN));
    }

    private int needSpend(String mode) {
        switch (mode) {
            case MODE_AIRPLAN:
                return 1400;
            case MODE_TRAVEL:
                return 500;
            case MODE_CAR:
                return 2400;
                //异常值
            default:
                return -1;
        }
    }
}
```

如同前文所说的，当需要添加可选的出行方案时，我们不得不去修改`needSpend()`函数中的`switch case`来达到目的；然而这样并不利于后期的维护。接下来，试着使用策略模式来使实现这个简单的出行案例。

###策略模式代码

既然，出行方案是可选的策略，就可以先抽象出一个出行策略的接口，包含`needSpend()`方法，返回出行花费的方法

```
public interface ITravelStrategy {
    public int needSpend();
}
```

接着，实现各种具体出行方案需要的类，在`needSpend()`方法返回该出行方案所需的费用。

```
/** 飞机出行 */
public class AirPlanStrategy implements ITravelStrategy {
    @Override
    public int needSpend() {
        return 1400;
    }
}

/** 火车出行 */
public class TrainStrategy  implements ITravelStrategy {
    @Override
    public int needSpend() {
        return 500;
    }
}

/** 自驾出行 */
public class CarStrategy  implements ITravelStrategy {
    @Override
    public int needSpend() {
        return 2400;
    }
}
```

这里，我们需要的策略就已经完成了，就等着我们选一种方案，看看所需要的花费。我们创建一个类，和`Strategy`组合使用来获取各种出行方的花费，并在`printSpend()`方法中打印出行所需的花费。

```
public class LetGo {
    ITravelStrategy mTravelStrategy;
    
    public LetGo(@NonNull ITravelStrategy strategy){
        mTravelStrategy = strategy;
    }
    
    public void printSpend(){
        Log.i(TAG, "出行花费: ￥" + mTravelStrategy.needSpend());
    }
}
```

以上的代码就可以完成自驾游花费的输出，策略模式我们就完成了。现在代码是不是比之前使用`switch case`实现的代码结构更加清晰、简洁。如果需要知道更多的出行方式的花费，只需要实现再实现`ITravelStrategy`接口，替换掉传入`LetGo`构造器的参数即可。

```
//输出自驾游花费
LetGo letGo = new LetGo(new CarStrategy()):
letGo.printSpend();
```

##3.总结

策略模式，其实可以简单地理解成，将过多的`switch case`中`case`的代码封装成一个个具有共性的对象，需要什么我们就直接使用什么；对于这种共性的实现就利用`interface`或者是抽象类来实现。这从对代码的封装以及解耦的角度来理解，可能会更加容易理解。

###使用场景

* 针对同一类问题的多种处理方式，仅仅是具体行为有差别时
* 需要安全的封装多种同一类型的操作时
* 出现同一抽象类的多个子类，而有需要使用`switch case`或`if else`来选择具体子类时

###优点

* 结构清晰、简单直观
* 耦合度低，方便扩张
* 操作封装更加彻底，数据更安全

###缺点

* 使用者必须熟知具策略的使用方式
* 随着策略的增加，具体的策略子类也会变得更多


  
