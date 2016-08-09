title: 设计模式之工厂方法模式
date: 2016-01-12 16:36:29
categories: [设计模式]
tags: [工厂方法模式]
---
**定义一个用于创建对象的接口，让子类决定实例化哪个类。工厂方法使一个类的实例化延迟到其子类**<!--more-->

# 工厂方法模式和简单工厂模式

工厂方法模式实际上算是简单工厂模式的衍生。简单工厂模式的优点在于工厂类包含必要逻辑判断，根据客户选择条件动态实例化相关的类，去除了客户端与具体产品的依赖。但是，当需求改变，有新方法需要实例化时，就需要在工厂类里添加新的分支。这违反了开放-封闭原则。

而工厂方法模式则解决了这一问题。客户端需要自己决定实例化哪个工厂，把简单工厂的内部逻辑判断移到了客户端代码来进行，把新增功能时，本来要修改工厂类的，改为了修改客户端。

# 案例分析

从实现一个简单的加减乘除的运算器来说起。

1. 首先，创建一个抽象的操作类，让要实现具体操作的类继承它
```
public abstract class Operation {

	protected double a;
	protected double b;
	public double getA() {
		return a;
	}
	public void setA(double a) {
		this.a = a;
	}
	public double getB() {
		return b;
	}
	public void setB(double b) {
		this.b = b;
	}
	public abstract double getResult();
}
```
2. 创建具体操作类，这边我们只创建加法和减法
```
public class Add extends Operation{

	@Override
	public double getResult() {
		return a+b;
	}

}

public class Sub extends Operation{

	@Override
	public double getResult() {
		return a-b;
	}

}
```
3. 创建一个工厂接口
```
public interface IFactory {
	Operation createOperation();
}
```
4. 创建加法工厂和减法工厂实现工厂接口
```
public class AddFactory implements IFactory{
	@Override
	public Operation createOperation() {
		return new Add();
	}
}

public class SubFactory implements IFactory{
	@Override
	public Operation createOperation() {
		return new Sub();
	}
}
```
5. 在客户端的实现方式为
```
public static void main(String[] args) {
		IFactory factory = new AddFactory();
		Operation operation = factory.createOperation();
		operation.setA(123);
		operation.setB(3);
		System.out.println(operation.getResult());
	}
```
运行结果
```
126.0
```
# 工厂方法模式结果图

![工厂方法模式结构图](http://7xpi7i.com1.z0.glb.clouddn.com/%E5%B7%A5%E5%8E%82%E6%96%B9%E6%B3%95%E6%A8%A1%E5%BC%8F%E7%BB%93%E6%9E%84%E5%9B%BE.jpg)

如果将案例和图一一对应的话，图中的Creator及案例中的IFactory接口，而ConcreteCreator对应的是加法和减法工厂，而ConcreteProudct则是加法减法的具体实现，Product则是Operation操作类。如果我们要添加乘法和除法，只需要添加它们的操作类和对应的工厂，就可以在客户端中通过相应的工厂来实例化对象进行使用了。

可以看出，工厂方法模式的工厂类和具体操作类的个数是一致的，每增加一个操作类，就需要增加一个工厂。

# 工厂方法的优缺点总结

- 优点

1. 用户只需要知道所需产品的具体工厂，无需关心创建过程
2. 符合开放-封闭原则

- 缺点

1. 每次增加一个新的产品，都需要增加新的具体类和工厂，在一定程度上增加了系统复杂度。