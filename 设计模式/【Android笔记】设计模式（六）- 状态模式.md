![](https://upload-images.jianshu.io/upload_images/595349-6dd228cf6cee0592.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##1.简述

**状态模式**属于行为模式，其定义就是：类的内部状态改变时，可以改变它的行为。可能描述的有点模糊，举个栗子（真就是举个栗子）

![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1521774782282&di=da794dbabe316fb3bcc445a752503a34&imgtype=0&src=http%3A%2F%2Fi2.hexunimg.cn%2F2016-04-22%2F183473690.jpg)

Android系统在未root时，是无法卸载系统预装应用的；root成功，开启root权限之后，不仅可以卸载系统预装应用，还可以使用xposed，简直就是可以为所欲为啊！这就可以看出，Android系统在root的状态变更时，影响到了自身的某些行为，进而某些行为也相对应的发生了改变。

![状态模式](https://upload-images.jianshu.io/upload_images/595349-fd09103ca50389e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从UML来看，结构似乎和策略模式是一模一样的，但是两种模式的摸底和本质却是完全不同的。策略模式中的策略（或者说是行为）是因为根据外界需要而使用不同的策略来完成需求；而状态模式中，是因为自身状态改变，而影响了自身的一系列行为的改变。虽然这个状态的变更可能是外界引发的。

> PS：看了书上和一些文章的描述，其中都提到一句话：“前者行为是彼此独立、可以相互替换的，后者是不可以相互替换的”。对于这句话并没有很好地理解，前者中策略就可以理解为行为，也就是说策略是独立、可以相互替换的；后者的状态其实也是相互独立的，也可以相互替换，所以并没有很好地理解这句话的意思。
> **如果有明白的朋友，还望指点迷津。**

##2.简单实现

简书中，经常使用的收藏和关注的功能，然而没有登录会提示去登陆，功能是无法使用的。实现收藏功能很容易出现以下代码：

```
public void collect(Context context){
	if(isLogin()){
		//收藏
	}else{
		//去登陆
	}
}
```

如果还需要添加关注功能的话，又需要再次判断是否登录，一旦需要判断登录的功能一多，这种coding方式就会成为噩梦。

![惊恐](https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=2906480513,1562664384&fm=27&gp=0.jpg)

我们需要优雅的解决这个问题。

![](https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=3918481685,3443115862&fm=27&gp=0.jpg)

我们还是正经的继续codeing吧！状态模式可以很好地避免这种情况，首先我们需要抽象出一个公共行为的一个接口`UserState`，其中包含`collect()`以及`follow()`方法。

```
public interface UserState {
	/** 收藏行为 */
    public void collect(Context context);
    /** 关注行为 */
    public void follow(Context context);
}
```

对应**登录**和**未登录**两种状态会有两种不同的实现方式，如下

```
//登录状态
public class LoginState implements UserState {
    @Override
    public void collect(Context context) {
        Toast.makeText(context, "收藏成功", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void follow(Context context) {
        Toast.makeText(context, "关注成功", Toast.LENGTH_SHORT).show();
    }
}

//未登录状态
public class LogoutState implements UserState {
    @Override
    public void collect(Context context) {
        Toast.makeText(context, "请登录", Toast.LENGTH_SHORT).show();
    }

    @Override
    public void follow(Context context) {
        Toast.makeText(context, "请登录", Toast.LENGTH_SHORT).show();
    }
}
```

接下来，我们需要一个类来关联登录状态与外界，并为外界提供`collect()`函数和`follow()`函数对应的调用；并将`LoginState`和`LogoutState`结合起来。

```
public class UserContext{
    private final UserState LOGIN = new LoginState();
    private final UserState LOGOUT = new LogoutState();
    private UserState mState = LOGOUT;

    /** 登录，供外部调用 */
    public void login(){
        mState = LOGIN;
    }

    /** 退出登录，供外部调用 */
    public void logout(){
        mState = LOGOUT;
    }

    /** 调用mState的coolect() */
    public void collect(Context context) {
        mState.collect(context);
    }

    /** 调用mState的follow() */
    public void follow(Context context) {
        mState.follow(context);
    }

    /** 此方法判断不太严谨，请忽略 */
    public boolean isLogin(){
        return mState instanceof LoginState;
    }

}
```

`UserContext`提供给外部`collect()`和`follow()`的同时，还提供`login()`和`logout()`两个方法给外部调用来更改`UserContext`内部的状态。这里主要是实现状态模式，对于用户的这个案例来说，结合单例模式使用更好。

> 从某种程度上来说，提供`login()`和`logout()`两个方法是为了防止外界篡改内部的行为，因为外部无法传入自己实现的`UserState`对象，也就无法修改`UserContext`对应状态的行为。
> 
> 确实也看到有些文章，在设置状态时，状态是由外部传入的，这种写法觉得不太合适，所以这里单独说明。

###扩展思考

其实对于实际情况来说，用户的操作行为可能远远不止这两个方法，若还有需要状态判断登录状态的行为，我们可以继续在`UserState`接口中为新增的行为添加相应的方法；但是，我们也可以预见，`UserState`接口将会越来越庞大。

这种问题的话，也可以将每个行为独立成一个接口，进而让`UserState`继承各个行为接口，或者将`UserState`改成抽象类实现各个行为接口。

```
//独立的收藏的接口
public interface ICollect {
    public void collect(Context context);
}

//独立的关注的接口
public interface IFollow {
    public void follow(Context context);
}

//所有行为接口的集合
public interface UserState extends ICollect,IFollow{
    
}

```
**以上纯属个人见解**

> 虽然个人感觉并没有实际解决`UserState`接口过于庞大的问题

##3.总结

状态模式的关键点在于不同的状态下，对于同一行为有不同的响应，避免使用`if-else`或`switch-case`区分状态所带来的代码臃肿；在保持代码结构的清晰的同时还保证了扩展和维护性。

随着状态的增加，系统类和对象个数也会增加，使用不当将导致陈旭结构和代码混乱，那就是得不偿失了。在行为受状态约束的时候使用状态模式，状态最好**不要超过5个**。其实也可以看出来，状态模式对于开闭模式的支持并不是太好增加新的状态类需要修改那些负责状态装换的源代码，否则无法切换到新增的状态模式，而且修改某个状态类的行为也需要修改对应类的源代码。
