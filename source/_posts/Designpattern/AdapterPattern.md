title: 设计模式之适配器模式
date: 2016-01-24 20:27:12
categories: [设计模式]
tags: [适配器模式]
---
**将一个类的接口转换成客户希望的另外一个接口。Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。**<!--more-->

适配器模式，说白了就是类似两个人言语不同，找一个中间人来进行翻译交流，而这个中间人就是适配器了。

在Gof设计模式中，适配器模式有两种类型：
- 类适配器模式：通过多重继承对一个接口与另一个接口进行适配。
- 对象适配器模式：创建一个对象实例。在这种情况下，适配器调用该对象的方法。

***Java不支持多重继承，本文讲的是对象适配器模式***。

# 适配器模式类图

![适配器结构图](http://7xpi7i.com1.z0.glb.clouddn.com/%E9%80%82%E9%85%8D%E5%99%A8%E6%A8%A1%E5%BC%8F%E7%BB%93%E6%9E%84%E5%9B%BE.png)

这个图也很好理解，即创建一个Adapter进行接口的转换。

- Target：期待得到的接口。
- Adapee：需要适配的接口。
- Adapter：把Adapee转换成Target。

# 案例分析

在篮球比赛中，如果新来了一个外籍球员，一般都需要一个翻译来帮忙球员理解教练的指令。在这里，翻译就是一个适配器，把教练的命令翻译给外来球员听。在这里，我们把教练的指令分为两种，分别为进攻（attack）和防守（defense）。

1. 定义一个Play抽象类，这是一个Target，即客户端要使用的。
```
public abstract class Player {
	protected String name;
	public Player(String name) {
		this.name = name;
	}
	/**
	 * 进攻
	 */
	public abstract void attack();
	/**
	 * 防守
	 */
	public abstract void defense();
}
```

2. 定义本地的中锋和外来中锋，本地中锋没有语言问题，可以直接理解命令，即可直接继承Player类,外来中锋有自己的一套进攻防守的命令，不能直接理解本地的命令，所以不能直接继承Player类。
```
/**
 * 本地中锋
 */
public class Center extends Player {

	public Center(String name) {
		super(name);
	}

	public void attack() {
		System.out.println("中锋"+name+"-->进攻");
	}

	public void defense() {
		System.out.println("中锋"+name+"<--防守");
	}

}

/**
 * 外籍中锋
 */
public class ForeignCenter {
	String name;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}
	public void jingong(){
		System.out.println(name +"--->进攻");
	}
	public void fangshou(){
		System.out.println(name +"<---防守");
	}
}
```

3. 这时，我们的中间人——翻译，就该出来帮忙了，中间人也是可以直接听懂命令的。所以，可直接继承Player类。
```
public class Translator extends Player {
	ForeignCenter fc = new ForeignCenter();
	public Translator(String name) {
		super(name);
		fc.setName(name);
	}
	//调用外籍中锋的进攻方法。
	public void attack() {
		fc.jingong();
	}
	//调用外籍中锋的防守方法。
	public void defense() {
		fc.fangshou();
	}
}
```
4. 这样，我们的教练(Client)就可以直接发号施令了。
```
public static void main(String[] args) {
	Player c= new Center("中锋");
	c.attack();
	c.defense();

	Player c2 = new Translator("外籍中锋");
	c2.attack();
	c2.defense();
}
```
5. 结果为：
```
中锋中锋-->进攻
中锋中锋<--防守
外籍中锋--->进攻
外籍中锋<---防守
```

# 总结

## 使用场景

一般，在系统的数据和行为都正确，而接口不符时，我们就该考虑使用适配器模式，目的是使控制范围外的一个原有对象与某一个接口匹配。适配器模式主要用于希望复用某些现存的类，但接口与复用环境要求不一致的情况。**适配器模式的本质是转换匹配（手段），复用功能（目的）**。

## 优点

- 将Target和 Adapee解耦。
- 增加了类的复用性和拓展性，将具体实现封装在适配器类中，对Client来说是透明的，且提高了适配器的复用。
- 符合开闭原则，灵活性强。

## 缺点

- 一次最多只能适配一个适配者类，有一定局限性。
- 过多的使用适配器会让系统零乱，不容易整体把握。


