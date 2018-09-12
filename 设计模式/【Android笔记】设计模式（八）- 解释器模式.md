![](https://upload-images.jianshu.io/upload_images/595349-6dd228cf6cee0592.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##1.简述

**解释器模式（Interpreter Pattern）**，实际应用中较少用到的行为模式。主要作用就是提供解释语言的语法或表达式的能力，从作用上来说，注定实际开发过程中会使用的少，毕竟很少有人需要构建一套自己的语法来解析吧！但是，这并不表示解释器模式我们可以忽略掉。


##2.实现

###解释器模式UML图

![解释器模式.png](https://upload-images.jianshu.io/upload_images/595349-12a440af6bd27bb4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* **Context**：上下文环境，包含解释器之外的全局信息
* **Client**：客户端，解析表达式，构建语法树，执行具体的解释操作等
* **AbstractExpression**：抽象表达式，声明一个抽象的解释操作弗雷，并定义一个抽象的解释方案，其具体的实现在各个具体的子类解释器中完成。
* **TerminalExpression**：终结符表达式，实现文法中终结符有关的解释操作。文法中每一个终结符都有一个具体的终结表达式与之对应。
* **NonterminalExpression**：非终结表达式，实现文法中非终结符有关的解释操作。

其中`AbstractExpression`的`interpret()`是抽象的解析方法，参数是上下文的环境，而`interpret()`方法的具体实现则由`TerminalExpression`和`NonterminalExpression`实现。

###具体实现

如下我们通过对算术表达式的解释来看一个解释器模式的实现, 如表达式`m+n+p`中,如果我们使用解释器模式对该表达式进行解释，那么`m`、`n`、`p`代表的三个字母可以看成是终结符号，而`+`代表的运算符则可以看成是非终结符号。

先创建抽象解释器，表示数学运算

```
public abstract class ArithmeticExpression {
	public abstract int interptet();
}
```

解释器中定义了`interptet()`方法，`ArithmeticExpression`有两个子类，分别是`NumExpression`和`OperatorExpression`

```
/** 对数字进行解释 */
public class NumExpression extends ArithmeticExpression {
	private int num;
		
	public NumExpression(int num) {
		this.num = num;
	}
		
	@Override 
	public int interptet() {
		return num;
	}
}

/** 对运算符进行解释 */
public abstract class OperatorExpression extends ArithmeticExpression {
	protected ArithmeticExpression mArithmeticExpression1,mArithmeticExpression2;
	
	public OperatorExpression(ArithmeticExpression arithmeticExpression1,ArithmeticExpression arithmeticExpression2) {
		mArithmeticExpression1 = arithmeticExpression1;
		mArithmeticExpression2 = arithmeticExpression2;
	}
}
```

基础的类已经完成了，如果需要处理加法运算还需要继承`OperatorExpression`并实现`interptet()`方法来实现加法运算器`AdditionExpression`

```
public class AdditionExpression extends OperatorExpression {
	public AdditionExpression(ArithmeticExpression arithmeticExpression1,ArithmeticExpression arithmeticExpression2) {
		super(arithmeticExpression1, arithmeticExpression2);
	}
	
	@Override
	public int interptet() {
		return mArithmeticExpression1.interptet() + mArithmeticExpression2.interptet();
	}
}
```

还差一个业务逻辑处理类

```
public class Calculator {

    //声明一个Stack栈，存储并操作所有相关的解释器
    protected Stack<ArithmeticExpression> mArithmeticExpressionStack = new Stack<>();

    public Calculator(String expression) {
        //声明两个ArithmeticExpression类型的临时变量，存储运算符左右两边的数字解释器
        ArithmeticExpression arithmeticExpression1, arithmeticExpression2;

        //根据空格分隔表达式字符串
        String[] elements = expression.split(" ");

        //循环遍历表达式元素数组
        for (int i = 0; i < elements.length; ++i) {
            switch (elements[i].charAt(0)) {
                //如果是加号
                case '+':
                    //将栈中的解释器弹出作为运算符号左边的解释器
                    arithmeticExpression1 = mArithmeticExpressionStack.pop();

                    //同时将运算符号数组下标下一个元素构造为一个数字解析器
                    arithmeticExpression2 = new NumExpression(Integer.valueOf(elements[++i]));

                    //通过上面两个数字解释器构造加法运算解释器
                    mArithmeticExpressionStack.push(new AdditionExpression(arithmeticExpression1, arithmeticExpression2));
                    break;
                default:
                    //如果不是运算符，则是数字，直接构造数字解释器并压入栈
                    mArithmeticExpressionStack.push(new NumExpression(Integer.valueOf(elements[i])));
                    break;
            }
        }
    }

    //计算结果
    public int calculate() {
        return mArithmeticExpressionStack.pop().interptet();
    }
}
```

这里需要注意的是，为了简化逻辑，在约定的表达式的每个元素之间必须使用空格隔开，如`123 + 32 + 666`这种形式的表达式，这样才能在`Calculator`中使用空格来拆分字符串。

如果想引入减法运算，我们只需要定义一个减法解释器

```
public class SubtractionExpreesion extends OperatorExpression{
	public SubtractionExpreesion(ArithmeticExpression arithmeticExpression1,ArithmeticExpression arithmeticExpression2) {
		super(arithmeticExpression1, arithmeticExpression2);
	}
	
	@Override
	public int interptet() {
		return mArithmeticExpression1.interptet() - mArithmeticExpression2.interptet();
	}
}
```

在`Calculator`的`switch`中添加如下代码即可

```
case "-":
	arithmeticExpression1 = mArithmeticExpressionStack.pop();
	arithmeticExpression2 = new NumExpression(Integer.valueOf(elements[++i]));
	
	mArithmeticExpressionStack.push(new SubtractionExpreesion(arithmeticExpression1, arithmeticExpression2));
	break;
```

##3.小结

这里，我们能看出来解释器模式灵活性强，但是这是对于相对简单的语言；如果需要加入乘除取余等等，一并进行混合预算的话还需要考虑不同符号的运算优先级逻辑处理，所以在“简单的语言”中适用解释器模式。其实解释器模式的本质就是，将复杂的问题模块化，分离实现、解释执行。






