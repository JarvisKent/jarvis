title: 设计模式之简单工厂模式
date: 2016-01-06 09:47:44
categories: [设计模式]
tags: [工厂模式]
toc: false
---
为了尽量使自己的代码质量提高、便于维护，开始着手研究设计模式。从很简单的简单工厂模式开始。<!--more-->

简单工厂模式又叫做「静态工厂方法」（Static Factory Method）模式。是通过专门定义一个工厂类来负责创建其他类的实例，被创建的实例通常都具有共同的父类。

![UML类图](http://7xpi7i.com1.z0.glb.clouddn.com/%E7%AE%80%E5%8D%95%E5%B7%A5%E5%8E%82%E6%A8%A1%E5%BC%8FUML%E5%9B%BE.jpg)

简单工厂的优点在于包含必要的判断逻辑，能根据外界给定信息来决定创建哪个类的对象，有利于整体软件体系结构的优化。

它的缺点也比较明显，工厂类一旦出问题，所有客户端就都有麻烦了。当新增加产品时，必须修改工厂类，这违反了“开放封闭原则”。

了解优缺点的作用在于日后我们使用的时候可以根据它的优缺点来判断是否适合使用在需要的场景汇中。

接下来，我们举个例子来通过代码了解一下它的应用。实现一个简单的计算器功能，要求是输入两个数值及计算符号，计算出要求的数值。

首先，我们要创建一个运算基类即上图中的抽象产品类，它需要包含两个数值及运算后的返回结果方法：
```
public class Operation{
	private double num_1 = 0;
	private double num_2 = 0;
	
	public double getNum_1() {
		return num_1;
	}

	public void setNum_1(double num_1) {
		this.num_1 = num_1;
	}

	public double getNum_2() {
		return num_2;
	}

	public void setNum_2(double num_2) {
		this.num_2 = num_2;
	}

	public double GetResult(){
		double result = 0;
		return result;
	}
}
```

接着，我们需要创建计算类继承于基类即上图中的具体产品，如添加一个加法计算类
```
public class Add extends Operation{
		@Override
	public double GetResult() {
		double result = getNum_1()+getNum_2();
		return result;
	}
}
```
其他计算类也是如此创建，这边就不列出代码了。

接着就是创建工厂类了，代码如下：
```
public class Factory {
	public static  Operation createOperation(String operate){
		Operation oper = null;
		switch (operate) {
		case "+":
			oper = new Add();

			break;

		case "-":
			oper = new Sub();
			
			break;
		case "*":
			oper = new Mul();
			
			break;
		case "/":
			oper = new Div();
			
			break;
		default:
			break;
		}
		
		return oper;
	}
}

```
当我们要添加新的功能，如平方根的话，即先见一个平方根计算类并实现，然后在工厂类中进行实例化，这样就可以在客户端中进行使用了。
使用方法如下:
```

Operation operate = Factory.createOperation("+");
operate.setNum_1(1);
operate.setNum_2(2);
double result = operate.GetResult()
```
这边得到的result就是两个数的和了。

OK,简单工厂的使用方法大致就是这个样子。





