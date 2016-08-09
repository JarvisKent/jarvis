title: 设计模式之桥接模式
date: 2016-02-04 12:34:17
categories: [设计模式]
tags: [桥接模式]
---
**将抽象部分与它的实现部分分离，使它们都可以独立地变化。**<!--more-->

# 桥接模式结构图

![桥接模式结构图](http://7xpi7i.com1.z0.glb.clouddn.com/%E6%A1%A5%E6%8E%A5%E6%A8%A1%E5%BC%8F%E7%BB%93%E6%9E%84%E5%9B%BE.jpg)

**Abstraction**：用于定义抽象类接口，是一般抽象类，其中定义了一个Implementor类型的对象并可以维护该对象，它与Implementor之间具有关联关系，既可以包含抽象业务方法，也可以包含具体业务方法。

**RefinedAbstraction**：扩充由Abstraction定义的接口，通常情况下不再是抽象类而是具体类，它实现了Abstraction中声明的抽象业务方法，可以调用在Implementor中定义的业务方法。

**Implementor**：定义实现类接口，不一定要与Abstraction接口完全一致，事实上这两个接口可以完全不同，一般Implementor仅提供基本操作，而Abstraction定义的接口可以做更多更复杂的操作。Implementor对基本操作进行声明，具体实现交给子类。通过关联关系，在Abstaction中不仅有自己的方法，还可以调用Implementor定义的方法，使用关联关系代替继承关系。

**ConcreteImplementor**：具体实现Implementor接口，在程序运行时将代替其父类对象，提供给抽象类具体的业务操作方法。

# 案例分析

电视厂商有很多，比如长虹、海尔、小米等等，电视的大小尺寸也是40、45、50都有。如何使用桥接模式动态结合呢。

1. 抽象一个电视生产厂商TelevisionMaker。
```
/**
 * 生产产商
 */
public abstract class TelevisionMaker {

	public abstract void produce(); 
}
```

2. 各个电视厂商继承抽象电视厂商。
```
public class ChangHong extends TelevisionMaker {

	public ChangHong(){
		System.out.println("长虹厂商");  
	}
	public void produce() {
		System.out.println("长虹厂商");  
	}

}

public class Haier extends TelevisionMaker {
	public Haier(){
		System.out.println("海尔厂商");  
	}
	public void produce() {
		System.out.println("海尔厂商");  
	}

}
```

3. 抽象电视类
```
public abstract class Television {
	protected TelevisionMaker televisionMaker;
	public abstract void teleView(TelevisionMaker televisionMaker);
}
```

4. 具体电视类
```
public class Inch21 extends Television {

	public void teleView(TelevisionMaker televisionMaker) {
		System.out.println("21寸电视");
	}

}

public class Inch29 extends Television {

	public void teleView(TelevisionMaker televisionMaker) {
		System.out.println("29寸电视");
	}

}
```

5. 客户端代码
```
public static void main(String[] args) {
	Inch29 i = new Inch29();
	i.teleView(new ChangHong());
}
```

6. 运行结果
```
长虹厂商
29寸电视
```

# 总结

## 适用场景

- 如果一个系统需要在构件的抽象化角色和具体化角色之间增加更多的灵活性，避免在两个层次之间建立静态的联系。 
- 设计要求实现化角色的任何改变不应当影响客户端，或者说实现化角色的改变对客户端是完全透明的。
- 一个构件有多于一个的抽象化角色和实现化角色，系统需要它们之间进行动态耦合。 
- 虽然在系统中使用继承是没有问题的，但是由于抽象化角色和具体化角色需要独立变化，设计要求需要独立管理这两者。

## 优点

- 分离抽象接口及其实现部分。桥接模式使用“对象间的关联关系”解耦了抽象和实现之间固有的绑定关系，使得抽象和实现可以沿着各自的维度来变化。所谓抽象和实现沿着各自维度的变化，也就是说抽象和实现不再在同一个继承层次结构中，而是“子类化”它们，使它们各自都具有自己的子类，以便任何组合子类，从而获得多维度组合对象。

- 在很多情况下，桥接模式可以取代多层继承方案，多层继承方案违背了“单一职责原则”，复用性较差，且类的个数非常多，桥接模式是比多层继承方案更好的解决方法，它极大减少了子类的个数。 

- 桥接模式提高了系统的可扩展性，在两个变化维度中任意扩展一个维度，都不需要修改原有系统，符合“开闭原则”。

## 缺点

- 违背了类的单一职责原则。

- 不同维度同时拓展会造成类的结构迅速变得庞大。