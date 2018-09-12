![](https://upload-images.jianshu.io/upload_images/595349-6dd228cf6cee0592.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##1.简述

Builder模式也就是建造者模式，先说定义，**将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。**

首先，将复杂对象的创建过程和部件的表示分离出来，其实就是把创建过程和自身的部件解耦，使得构建过程和部件都可以自用扩展，两者之间的耦合降到最低。然后，再是相同的构建过程可以创建不同的表示，相同的组合也可以通过不同的部件创建出不同的对象。

可能使用场景

* 相同的方法，不同的执行顺序，产生不同的结果时
* 多个部件（代码中就对应类的属性），都可以装配到一个对象中，但是产生的运行结果又不相同时
* 初始化一个对象特别复杂，参数多且很多参数都具有默认值时

这只是举几个比较合适的栗子而已

##2.实现

![](https://upload-images.jianshu.io/upload_images/595349-465cc196e0c64aae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* Product —— 产品的抽象类
* Builder —— 抽象Builder类，规范产品的组件
* ConcreteBuilder —— 具体的Builder类，实现具体的组建过程
* Director —— 统一组装过程

看到这里可能回觉得这个模式怎么和android平时使用的不太一样啊！Builder一般不是只有Product和Builder么？

关于这点，有度娘过一下Builder模式，基本介绍都是这种结构，相对标准，这个标准的意思就是帮助学习该设计模式，并不是指一种用法，毕竟设计模式还是需要灵活使用。而没有Director该环节的实现方式就是对Builder模式的一种简化。

这是一个手机配置简化的抽象类，设置CPU、OS以、内存大小以及运存大小

```
public abstract class Phone {
    protected String mCPU;
    protected String mOS;
    protected int mMemorySize;
    protected int mStorageSize;

    public abstract void setCPU();

    public abstract void setOS();

    public void setMemorySize(int memorySize) {
        this.mMemorySize = memorySize;
    }

    public void setStorageSize(int storageSize) {
        this.mStorageSize = storageSize;
    }

    @Override
    public String toString() {
        return "{ \n CPU : " + mCPU + "\n OS : " + mOS + 
	        " \n MemorySize : " + mMemorySize + "GB \n StorageSize : " + mStorageSize + "GB \n}";
    }
}
```

具体的IPhoneX的类，由于CPU和系统是固定，而内存和运存运存可选。

```
public class IPhoneX extends Phone {

    public  IPhoneX(){}

    @Override
    public void setCPU() {
        mCPU = "A11";
    }

    @Override
    public void setOS() {
        mOS = "iOS 11";
    }
}

```

抽象的Builder类，作为主要隔离作用的类，Phone的API的每一个方法都有对应的build方法。

```
public abstract class Builder {
    //设置CPU
    public abstract Builder buildCPU();
    //设置系统
    public abstract Builder buildOS();
    //设置运存大小
    public abstract Builder buildMemorySize(int memorySize);
    //设置储存大小
    public abstract Builder buildStorageSize(int storageSize);
    //创建一个Phone对象
    public abstract Phone create();
}
```

IPhoneXBuilder，具体的Builder类，稍作优化采用了链式API的方式。

```
public class IPhoneXBuilder extends Builder {

    private IPhoneX mIPhoneX = new IPhoneX();

    @Override
    public Builder buildCPU() {
        mIPhoneX.setCPU();
        return this;
    }

    @Override
    public Builder buildOS() {
        mIPhoneX.setOS();
        return this;
    }

    @Override
    public Builder buildMemorySize(int memorySize) {
        mIPhoneX.setMemorySize(memorySize);
        return this;
    }

    @Override
    public Builder buildStorageSize(int storageSize) {
        mIPhoneX.setStorageSize(storageSize);
        return this;
    }

    @Override
    public Phone create() {
        return mIPhoneX;
    }
}
```

Director类，负责构造Phone

```
public class Director {

    Builder mBuilder = null;

    public Director(Builder builder){
        mBuilder = builder;
    }

    public void construct(int memorySize,int storageSize){
        mBuilder.buildCPU()
            .buildOS()
            .buildMemorySize(memorySize)
            .buildStorageSize(storageSize);
    }
}
```

下面是测试代码

```
//构建器
Builder builder = new IPhoneXBuilder();
//Director
Director director = new Director(builder);
//封装构建过程
director.construct(6,256);
//构建Phone，输出相关信息
Log.i(TAG, builder.create().toString());
```

通过具体的`IPhoneXBuilder`类构建`IPhoneX`对象，`Director`封装了构建Phone对象的过程，隐藏构建的细节。`Builder`和`Director`两个部分起到了将对象的构建过程和对象的表示分离的作用。

之前也提到过，真是开发中`Director`类一般会省略，直接使用`Builder`，采用链式API进行组装，在使用Buidler模式的过程中更加有效。

##3.Android源码中得Builder模式实现
首先想到的就是`AlertDialog.Builder`,`AlertDialog.Builder`其实是`AlertDialog`的静态内部类，所有的dialog属性都暂存在`AlertController.AlertParams`的一个final对象中，以此来做到一个Builder对象只组装一次，却能构建出多个属性相同的dialog。

这里截取了`AlertDialog`中比较关键的代码，

```
public class AlertDialog extends AppCompatDialog implements DialogInterface {

	final AlertController mAlert;

	protected AlertDialog(@NonNull Context context, @StyleRes int themeResId) {
        super(context, resolveDialogTheme(context, themeResId));
        //构造AlertController对象
        mAlert = new AlertController(getContext(), this, getWindow());
    }
    
    //此处省略不知道多少代码。。。
    
    @Override
    public void setTitle(CharSequence title) {
        super.setTitle(title);
        mAlert.setTitle(title);
    }
    
    //******* 内部类Builder *******
    public static class Builder {
    	//存储AlertDialog的相关属性
        private final AlertController.AlertParams P;
        private final int mTheme;
        
        public Builder(@NonNull Context context, @StyleRes int themeResId) {
            P = new AlertController.AlertParams(new ContextThemeWrapper(
                    context, resolveDialogTheme(context, themeResId)));
            mTheme = themeResId;
        }
        
        //...
        
        public Builder setTitle(@StringRes int titleId) {
            P.mTitle = P.mContext.getText(titleId);
            return this;
        }
        
        //...
        
        public AlertDialog create() {
            // We can't use Dialog's 3-arg constructor with the createThemeContextWrapper param,
            // so we always have to re-set the theme
            final AlertDialog dialog = new AlertDialog(P.mContext, mTheme);
            //将设置到Builder中得属性设置到dialog的mAlert中，再由mAlert设置到view上
            P.apply(dialog.mAlert);
            dialog.setCancelable(P.mCancelable);
            if (P.mCancelable) {
                dialog.setCanceledOnTouchOutside(true);
            }
            dialog.setOnCancelListener(P.mOnCancelListener);
            dialog.setOnDismissListener(P.mOnDismissListener);
            if (P.mOnKeyListener != null) {
                dialog.setOnKeyListener(P.mOnKeyListener);
            }
            return dialog;
        }

        public AlertDialog show() {
            final AlertDialog dialog = create();
            dialog.show();
            return dialog;
        }
    }
}
```

还有一点值得关注，`AlertDialog`中的`AlertController`对象，是接收`Builder`成员变量`AlertController.AlertParams`中的各个参数。`AlertController`与`AlertController.AlertParams`不太相同的地方是，`AlertController`中又真是的View和属性设置，而`AlertController.AlertParams`只是作为一个暂时存放属性值得对象，在`apply()`方法调用时将相关的属性值传递给`AlertDialog`的`AlertController`对象，再由AlertController`对象设置到view上。

##4.总结

Builder模式在Android开发中也是比较常用的，通常作为配置类的构建器，将配置的构建和表示分离，同时也是将配置从使用中隔离出来，避免过多的setter方法。在Builder模式的实现中经常通过链式调用实现，达到通俗易懂的目的。

**优点：**

* 良好的封装性，使用者不用知道内部的实现细节
* 容易扩展，由于Builder的独立存在扩展不会影响原有逻辑

**缺点：**

* 会产生多余的Builder对象，可能还有Director对象，占用内存

> 像网络框架（或图片加载框架）的Config的构建，也是Builder模式所适用的场景。总的来说，设计模式还是需要明白设计的主要目的，才能更好地使用各种模式去解决实际问题。








