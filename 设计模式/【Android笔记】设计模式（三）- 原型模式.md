![](https://upload-images.jianshu.io/upload_images/595349-6dd228cf6cee0592.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##1.简述

直接上定义：**用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。**简单地理解，其实就是当需要创建一个指定的对象时，我们刚好有一个这样的对象，但是又不能直接使用，我会clone一个一毛一样的新对象来使用；基本上这就是原型模式。关键字：**Clone**。

这些场景可能派的上用场

* 当new一个对象时，非常繁琐复杂时，可以使用原型模式来进行复制一个对象。即使需求的变更，这些对象需要作出调整，我们依然拥有比较稳定一致的借口创建对象。
* 需要提供数据对象，同时有需要避免外部对数据对象进行修改。

> 关于使用场景，看了书上和一些资料的描述，感觉都没有描述的太清楚，以上是个人理解

###最简单的UML图

![](https://upload-images.jianshu.io/upload_images/595349-fe5c67c24d6377cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Client：使用者
Prototype：接口（抽象类），声明具备clone能力，例如java中得`Cloneable`接口
ConcretePrototype：具体的原型类

可以看出设计模式还是比较简单的，重点在于`Prototype`接口和`Prototype`接口的实现类`ConcretePrototype`。

##2.浅拷贝和深拷贝

浅拷贝是什么呢？一个对象通过赋值的形式直接传递的其实是对象在内存中的内存地址

```
ArrayList<String> a = new ArrayList();
ArrayList<String> b = a;
//当修改a时，b的值同样会被修改
```

以上的代码就是浅拷贝，从某种角度来说，这种浅拷贝的方式并不合适在原型模式中使用，更多情况下我们需要一个不会影响原始对象的一个新对象，也就需要使用到深拷贝。举个简单的例子：

```
ArrayList<String> a = new ArrayList();
ArrayList<String> b = a.clone();
//或者
ArrayList<String> c = new ArrayList(a);
```

上述代码的第一种方式，是使用`Object`类的`super.clone()`方法来使实现拷贝的过程；而第二种方式，是使用a再创建了一个对象并赋值给c，这样a和c只是两个值相同的两个对象。

```
//ArrayList的clone()方法代码
public Object clone() {
    try {
        ArrayList<?> v = (ArrayList<?>) super.clone();
        v.elementData = Arrays.copyOf(elementData, size);
        v.modCount = 0;
        return v;
    } catch (CloneNotSupportedException e) {
        // this shouldn't happen, since we are Cloneable
        throw new InternalError(e);
    }
}
```

> 需要注意的是，通过实现`Cloneable`接口的原型模式在调用`clone()`方法构造实例是并不一定比通过 new 操作速度块，只有当通过 new 狗仔队向较为耗时或者说成本较高时，通过clone方法才能获得效率上德提升。因此，在使用Cloneable是需要考虑构建对象的成本以及做一些效率上的测试。

##3.Android源码中的模型模式

这里我们参考的时`Intent`的`clone()`方法

```
public Object clone() {
    return new Intent(this);
}
```

代码不多，而且与`new ArrayList()`的这种方式有点类似，接下来我们看看这个构造函数具体做了什么

```
//为了能看明白一些，这里把相关的属性一并带上
private String mAction;
private Uri mData;
private String mType;
private String mPackage;
private ComponentName mComponent;
private int mFlags;
private ArraySet<String> mCategories;
private Bundle mExtras;
private Rect mSourceBounds;
private Intent mSelector;
private ClipData mClipData;
private int mContentUserHint = UserHandle.USER_CURRENT;
private String mLaunchToken;

/** 拷贝构造函数 */
public Intent(Intent o) {
    this.mAction = o.mAction;
    this.mData = o.mData;
    this.mType = o.mType;
    this.mPackage = o.mPackage;
    this.mComponent = o.mComponent;
    this.mFlags = o.mFlags;
    this.mContentUserHint = o.mContentUserHint;
    this.mLaunchToken = o.mLaunchToken;
    if (o.mCategories != null) {
        this.mCategories = new ArraySet<String>(o.mCategories);
    }
    if (o.mExtras != null) {
        this.mExtras = new Bundle(o.mExtras);
    }
    if (o.mSourceBounds != null) {
        this.mSourceBounds = new Rect(o.mSourceBounds);
    }
    if (o.mSelector != null) {
        this.mSelector = new Intent(o.mSelector);
    }
    if (o.mClipData != null) {
        this.mClipData = new ClipData(o.mClipData);
    }
}
```

我们可以看到，Intent并没有使用`super.clone()`方法，而是将原始对象作为构造函数的参数，然后再构造器内将原始对象的数据逐个拷贝了一遍，以此来完成拷贝。

##4.总结

原型模式的本质就是clone，可以解决构建复杂对象的资源消耗问题，能再某些场景中提升构建对象的效率；还有一个重要的用途就是保护性拷贝，可以通过返回一个拷贝对象的形式，实现只读的限制。

