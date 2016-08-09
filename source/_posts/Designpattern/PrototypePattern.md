title: 设计模式之原型模式
date: 2016-01-22 15:44:19
categories: [设计模式]
tags: [原型模式]
---
**用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象**<!--more-->

# 原型模式结构图

![原型模式结构图](http://7xpi7i.com1.z0.glb.clouddn.com/%E5%8E%9F%E5%9E%8B%E6%A8%A1%E5%BC%8F%E7%BB%93%E6%9E%84%E5%9B%BE.jpg)

- Client：客户端，提出创建对象的请求。
- Prototype：抽象原型，通常由一个Java接口或者Java抽象类实现，给出所有具体原型类所需的接口。
- ConcretePrototype：具体原型，即被复制的对象。此角色需要实现抽象原型所要求的接口

它向客户隐藏了创建对象的复杂性。客户只需要知道要创建对象的类型，然后通过请求就可以获得和该对象一模一样的新对象，无须知道具体的创建过程。

# 代码解析

原型模式中的拷贝分为"浅拷贝"和"深拷贝":
- 浅拷贝: 对值类型的成员变量进行值的复制,对引用类型的成员变量只复制引用,不复制引用的对象。
- 深拷贝: 对值类型的成员变量进行值的复制,对引用类型的成员变量也进行引用对象的复制。

## 浅拷贝

1. 创建一个原型类，实现Cloneable接口：
```
public class Prototype implements Cloneable {
	private String name;
	public String getName(){
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
	public Object clone(){
		try {
			return super.clone();
		} catch (CloneNotSupportedException e) {
			e.printStackTrace();
			return null;
		}
	}
}
```
Cloneable接口没有定义成员。它通常用于指明被创建的一个允许对对象进行位复制（也就是对象副本）的类。如果试图用一个不支持Cloneable接口的类调用clone( )方法，将引发一个CloneNotSupportedException异常。

2. 在客户端调用代码:
```
public static void main(String[] args) {

		Prototype pro = new Prototype();
		pro.setName("原型对象");
		Prototype pro1 = (Prototype) pro.clone();
		pro.setName("拷贝对象");

		System.out.println("prototype :"+pro.getName());
		System.out.println("clone : "+pro1.getName());
	}
```
3. 运行结果
```
prototype :拷贝对象
clone : 原型对象
```

## 深拷贝

1. 创建Prototype类:
```
public class Prototype {
	private String name;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
}
```
2. 实现Cloneable接口的Prototype:
```
public class ConcretePrototype implements Cloneable {
	private String id ;

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	private Prototype prototype;

	public Prototype getPrototype() {
		return prototype;
	}
	public void setPrototype(Prototype prototype) {
		this.prototype = prototype;
	}

	public Object clone(){
		try {
			return super.clone();
		} catch (CloneNotSupportedException e) {
			e.printStackTrace();
			return null;
		}
	}
}
```
3. 客户端调用代码：
```
public static void main(String[] args) {
	Prototype pro = new Prototype();
	pro.setName("原型对象");
	ConcretePrototype cp1 = new ConcretePrototype();
	cp1.setId("cp1");
	cp1.setPrototype(pro);
	
	ConcretePrototype cp2 = (ConcretePrototype) cp1.clone();
	cp2.setId("cp2");
	cp2.getPrototype().setName("拷贝对象");
	System.out.println("cp1         id---->"+cp1.getId());
	System.out.println("cp1   prototype---->"+cp1.getPrototype().getName());
	System.out.println("----------------------------");
	System.out.println("cp2         id---->"+cp2.getId());
	System.out.println("cp2   prototype---->"+cp2.getPrototype().getName());
}
```
4. 运行结果:
```
cp1         id---->cp1
cp1   prototype---->拷贝对象

----------------------------

cp2         id---->cp2
cp2   prototype---->拷贝对象
```

# 总结

原型模式是一种创建型设计模式,它通过复制一个已经存在的实例来返回新的实例,而不是新建实例。被复制的实例就是我们所称的原型,这个原型是可定制的。原型模式多用于创建复杂的或者耗时的实例, 因为这种情况下,复制一个已经存在的实例可以使程序运行更高效,或者创建值相等,只是命名不一样的同类数据。

## 优点

- 如果创建新的对象比较复杂时，可以利用原型模式简化对象的创建过程，同时也能够提高效率。
- 使用深克隆可以保持对象的状态。
- 原型模式提供了简化的创建结构。

## 缺点

- 在实现深克隆的时候可能需要比较复杂的代码。
- 需要为每一个类配备一个克隆方法，而且这个克隆方法需要对类的功能进行通盘考虑，这对全新的类来说不是很难，但对已有的类进行改造时，不一定是件容易的事，必须修改其源代码，违背了“开闭原则”。

## 使用场景

- 如果创建新对象成本较大，我们可以利用已有的对象进行复制来获得。