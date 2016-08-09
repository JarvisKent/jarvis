title: 设计模式之装饰模式
date: 2016-01-08 15:24:23
categories: [设计模式]
tags: [装饰模式]
---
**动态地给一个对象添加一些额外的职责**，以对客户端透明的方式扩展对象的功能，是继承关系的一个替代方案，提供比继承更多的灵活性。<!--more-->

从字面意思可以看出，是给一个对象添加额外的功能。一般情况，我们是通过生成子类来实现添加额外功能。而装饰模式是采用组合的方法，实现了在运行时动态拓展对象的功能，可以根据需要拓展多个功能，避免了单独使用继承带来的“灵活性差”的问题。且把不同的职责封装在不同职责类中的私有方法或属性中，每个职责类只关心自己的职责。对内开放，对外封闭。符合了“单一职责”和“开放——封闭”原则；同时也符合面向对象设计原则的“优先使用对象组合而非继承”。

# UML图

![装饰模式uml图](http://7xpi7i.com1.z0.glb.clouddn.com/%E8%A3%85%E9%A5%B0%E6%A8%A1%E5%BC%8F%E7%BB%93%E6%9E%84%E5%9B%BE.jpg)

- Component是定义一个对象接口，可以给这些对象动态地添加职责。
- ConcreteComponent是定义了一个具体的对象，也可以给这个对象添加一些职责。
- Decorator，装饰抽象类，继承了Component从外类来拓展Component类的功能，但对于Component来说，是无需知道Decorator的存在的。
- ConcreteDecorator是具体的装饰对象，起到给Component添加职责的功能。

接下来，我们通过一个代码示例来展示一下，装饰器是如何在代码中实现的。

# 代码示例

案例：我们要实现这样的功能：获取数据，然后对数据进行的操作有排序、去掉最大最小值、求平均值等。具体会进行什么操作，需根据需求进行设定。我们来看看如何用装饰模式进行实现。

1. 定义一个getData接口。
```
public interface Data{
	void Operation();
}
```
2. 定义一个具体对象getData。
```
public class getData implements Data{

	@Override
	public void Operation() {
		System.out.println("获取数据");		
	}

}
```
3. 接下来是装饰抽象类Decorator，即具体的操作要继承这个类
```
public class Decorator implements Data {
	protected Data d;
	public void Set(Data data){
		this.d = data;
	}
	@Override
	public void Operation() {
		if(d != null){
			d.Operation();
		}
	}
}
```
4. 接着实现具体操作了。
```
public class DecoratorA extends Decorator {

	@Override
	public void Operation() {
		super.Operation();
		System.out.println("对数据进行排序");
	}
}

public class DecoratorB extends Decorator {

	@Override
	public void Operation() {
		super.Operation();
		System.out.println("对数据进行求平均值");
	}
}

public class DecoratorB extends Decorator {

	@Override
	public void Operation() {
		super.Operation();
		System.out.println("对数据进行去最大最小值操作");
	}
}
```
5. 在客户端，我们就可以根据需求动态的添加数据的操作了
```
/**
* da   //排序
* db   //求平均值
* db   //去除最大最小值
*/
public static void main(String[] args) {

	getData getData = new getData();  //获取数据对象
	DecoratorA da = new DecoratorA(); 
	DecoratorB db = new DecoratorB(); 
	DecoratorC dc = new DecoratorC(); 
	
	//先排序，再去最大最小值，再求平均值
	da.set(getData);
	dc.set(da);
	db.set(dc);
	db.Operation();

}
```
6. 运行结果为
```
得到数据
对数据进行排序
对数据进行去最大最小值操作
对数据进行求平均值
```

装饰模式在给对象拓展功能上的灵活性高于继承。它允许系统根据需要动态决定，需要添加哪个“装饰”，而继承关系是静态的，在系统运行前就决定了。

在使用上，装饰模式比较不好的地方在于比继承产生更多的对象，可能会造成排错上的困难，特别是这些对象比较相像的时候。


# 备注

1. **单一职责:就一个类而言，应该仅有一个引起它变化的原因。**
2. **开放——封闭原则：软件实体（类、模块、函数等等）应该可以拓展，但是不可修改。**
