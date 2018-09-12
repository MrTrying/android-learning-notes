![](https://upload-images.jianshu.io/upload_images/595349-6dd228cf6cee0592.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##1.简述

**责任链模式（Chain of Responsibility）**，行为型设计模式之一。什么是责任链呢？这个链的形式更像是数据结构中的单链表，链中的每个节点都有自己的职责，同时也持有下一个节点的引用，属于自己职责范围内的请求就自行处理，并完成请求的处理；而不属于的职责就传递给下一个节点。每个节点都是如此循环，直至请求被处理或者已经没有处理节点。

这种设计模式是为了避免请求的发送者和接收者之间的耦合关系，而责任链就是中间的请求处理者，其中可能包括多个有可能处理请求的对象，并将这些对象炼成一条链。这样也使得请求发送者无需关心请求的处理细节和请求的传递。

![责任链模式.png](https://upload-images.jianshu.io/upload_images/595349-eea9ef31f92adee2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* Client：客户端，请求的发起者
* Handler：抽象处理者，声明一个请求方法，并在其中保持一个对下一个处理节点Handler对象的引用
* ConcreteHandler：具体处理角色，对请求进行处理；如果不能处理则将请求转发给下一个节点对象处理

> 这是一个基本的结构描述，实际应用中会有进一步的封装。

##2.案例实现

以公司正常请假为例，1天以内的假需要客户端部门主管签字，3天以内（不包含3天）需要技术部门主管签字，3天及以上就需要找CEO签字。

最简单的就是使用`if-else`实现，但是结构并不是那么美观，试着用责任链模式来实现。

首先是假条的类，包含姓名、请假原因和请假时间，使用`final`声明属性只是为了避免外部修改属性而已。

```
/** 假条的对象 */
public class LeaveNote {
    final String name;
    final String reason;
    final int leaveDays;

    public LeaveNote(String name, String reason, int leaveDays) {
        this.name = name;
        this.reason = reason;
        this.leaveDays = leaveDays;
    }

    public String getName() {
        return name;
    }

    public String getReason() {
        return reason;
    }

    public int getLeaveDays() {
        return leaveDays;
    }
}
```

接下来就是比较重要的`Handler`类，代码比较简单，下一个`Handler`的对象引用，再就是`handle()`和`setNextHandler()`方法，不多解释了，和之前的UML图中的的结构是一样的

```
public abstract class Handler {
    private Handler nextHandler;

    abstract void hand(int level);

    public void setNextHandler(Handler nextHandler){
        this.nextHandler = nextHandler;
    }
}
```

现在我们需要实现客户端主管、技术主管和CEO三个`Handler`的子类

```
public class ClientLeader extends Handler {

    @Override
    void hand(LeaveNote note) {
        if(note.leaveDays <= 1){
            Log.i(TAG, "客户端主管同意" + note.name + "请假");
        }else{
            nextHandler.hand(note);
        }
    }
}

public class TechnologyLeader extends Handler {
    @Override
    void hand(LeaveNote note) {
        if(note.leaveDays < 3){
            Log.i(TAG, "技术主管同意" + note.name + "请假");
        }else{
            nextHandler.hand(note);
        }

    }
}

public class CEO extends Handler {
    @Override
    void hand(LeaveNote note) {
        Log.i(TAG, "CEO同意" + note.name + "请假");
    }
}
```

接着我们就需要测试调用我们写好的代码了

```
public class Client {
    
    /** 测试方法 */
    public void test(){
        LeaveNote leaveNote = new LeaveNote("name","事假",3);
        requestLeave(leaveNote);
    }

	/** 具体的封装方法 */
    public void requestLeave(LeaveNote leaveNote){
        ClientLeader clientLeader = new ClientLeader();
        TechnologyLeader technologyLeader = new TechnologyLeader();
        CEO ceo = new CEO();

        clientLeader.setNextHandler(technologyLeader);
        technologyLeader.setNextHandler(ceo);

        clientLeader.hand(leaveNote);
    }
}
```

这里完成了，比较简单地责任链模式就完成了。

其实还有有想过，nextHandler作为构造函数的参数的形式传入，于是测试代码变成这个样子

```
public void requestLeave(LeaveNote leaveNote){
    CEO ceo = new CEO(null);
    TechnologyLeader technologyLeader = new TechnologyLeader(ceo);
    ClientLeader clientLeader = new ClientLeader(technologyLeader);

    clientLeader.hand(leaveNote);
}
```

看起来有点诡异的样子

![](https://upload-images.jianshu.io/upload_images/595349-235f063d7b86ad0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 其实还看到有前端的文章，其中有使用函数式编程来实现责任链模式，直接传入方法的形式来处理符合不同职责的对应处理方法。java本身是面向对象，没法这么实现，只得放弃。

##3.Android源码中责任链的实现



##4.总结

**责任链的优点就在于请求者和接受者松散耦合，以及能动态组合职责。**

例子中可以看出，请求者不知道接受者是谁，也不知道具体的处理过程，只需要发出请求就行了。而对于每个职责对象来说，也不关心请求者和其他的职责对象（虽然持有下一个职责对象的引用），只负责处理自己职责的部分，其他的就交给其他的职责对象去处理。

动态组合职责则是利用对于职责的拆分，可以灵活的组合形成责任链，从而可以灵活的分配职责，也可以灵活的实现职责对象。

其实会发现，为了处理一个请求，我们可能会**创建很多个职责对象**，但是最后实际执行的最多只有一个职责对象（甚至没有），为了兼容更多职责就需要更多地职责对象。可见细化职责的同时，我们也在不断的增加对象，这不是一个好现象；而且在职责对象中，可能需要提供默认处理，不然请求很可能不会被责任链中的任何一个职责对象处理。

> 分离职责，动态组合

**PS：其实刚开始学习责任链模式的时候，我在想：“这种设计模式并没有做到很好地解耦啊！每个Handler还需要持有下一个Handler对象的引用，这不是造成更高的耦合度了么！”。之后才看明白，责任链的解决的时发出请求的一方和接受请求的一方的耦合度的问题，而处理这一切的`Handler`就是解决方案，所以`Handler`之间的耦合基本算是内部的。**

