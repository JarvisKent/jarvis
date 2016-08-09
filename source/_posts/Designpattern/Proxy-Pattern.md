title: 设计模式之代理模式
date: 2016-01-10 11:14:59
categories: [设计模式]
tags: [代理模式]
---
**为其他对象提供一种代理以控制对这个对象的访问。**<!--more-->
可以理解为，不想让客户端直接引用某个对象，而创建一个代理对象来当作客户端与目标对象的中介。我们不仅可以通过代理对象去掉客户不能看到的内容和服务，也可以添加一些客户需要的额外服务。

# 应用案例

假设一个案例，小明是个花痴富二代，每次见到漂亮的美女都会买各种东西叫跟班拿去送给美女讨欢心。

1. 首先，我们要定义一个接口，提供送礼物的接口。
```
/**
 * 实现一个送花和送情书的接口
 */
public interface GiveGift {
	void giveFlower();
	void giveLoveLetter();
}
```
2. 接着，让小明和他的根本都来实现这个接口
```
/**
 * 小明实现了接口，干他想干的事
 */
public class XiaoMing implements GiveGift{

	private String girlName;
	public XiaoMing(BeautifulGirl girl){
		this.girlName = girl.getName();
	}
	@Override
	public void giveFlower() {
		System.out.println("小东送"+girlName+"的花");
	}

	@Override
	public void giveLoveLetter() {
		System.out.println("小东写给"+girlName+"的情书");
	}

}

/**
 * 跟班实现了接口，帮小明干他想干的事
 * @author Administrator
 *
 */
public class Genban implements GiveGift{

	private GiveGift girl;
	public Genban(BeautifulGirl girl){
		this.girl = new XiaoMing(girl);
	}
	@Override
	public void giveFlower() {
		girl.giveFlower();
	}

	@Override
	public void giveLoveLetter() {
		girl.giveLoveLetter();
	}

}
```
3. 这样，在漂亮女孩出现时，小明就可以让跟班赶紧去送礼物了。
```
/**
 * 出现漂亮的女孩，跟班就开始献花、送情书了，而实际上是替小明送的
*/
public static void main(String[] args) {
	BeautifulGirl girl = new BeautifulGirl();
	girl.setName("美女");
	System.out.println("--------美女出现了--------");
	Genban genban = new Genban(girl);
	genban.giveFlower();
	genban.giveLoveLetter();
}
```
4. 运行效果如下
```
--------美女出现了--------
小明送美女的花
小明写给美女的情书
```

通过这样的方式，我们就实现了一个简单的代理模式应用。

# 代理模式的结构图

看一下一目了然的UML结构图。
![代理模式结构图](http://7xpi7i.com1.z0.glb.clouddn.com/%E4%BB%A3%E7%90%86%E6%A8%A1%E5%BC%8F%E7%BB%93%E6%9E%84%E5%9B%BE.png)

# 代理模式中涉及到的角色

代理模式涉及到的几个角色分别是：
- Subject（抽象角色）：定义了真实角色和代理角色的共同接口。在任何需要使用到真实角色的地方，都可以通过代理角色来使用。
- RealSbuject（真实角色）：定义了代理角色所代表的具体对象。
- Proxy（代理角色）：内部含有对真实角色的引用，从而可以操作真实角色，同时提供了与真实角色相同的接口，以便代替真实角色；同时可以在执行真实角色操作时，附加其他操作，相当于对真实角色进行封装。

# 代理模式的应用

代理模式在不直接操作对象的情况下，可以对此对象进行访问。一般会应用于以下几种情况。

## 远程代理（Remote Proxy）

远程服务的代理常常被称为远程代理。即为一个对象在不同的地址空间提供局部代表。这样可以隐藏一个对象存在于不同地址空间的事实。

## 虚拟代理（Virtual Proxy）

处理本地资源的代理被称为虚拟代理。即根据需要创建一个开销很大的对象。通过它来存放实例化需要很长时间的真实对象。
例如：当我们打开一个有大量文字和图片的网页时，可以快速的打开，文字先显示出来，然后图片再一张一张的显示出来。

## 安全代理（保护代理 Protection Proxy）

强制控制访问时权限的代理。

## 智能指引（Smart Reference）

是指当调用真实对象时，执行一些附加操作。
例子：计算真实对象引用次数，当对象没有引用时，自动释放；或第一次
引用一个持久对象时，装入内存；或访问实际对象前，检查是否锁定它，
确保其他对象不改变它。

## Copy-on-Write代理

虚拟代理的一种，把复制（或者叫Clone）拖延到只有客户端需要才去执行。

# 代理模式的优缺点

## 优点
- 代理模式能将用于真正被调用的对象隔离，在一定程度上降低了耦合。
- 代理对象在客户端和目标对象将起到中介的作用，也是对目标对象的保护。
- 代理对象可以在对目标对象发出请求前进行额外操作，如权限检测等。

## 缺点

- 在客户端和真实对象之间增加了代理对象，会造成请求的处理变慢
- 实现代理类需要额外的工作，增加了系统的实现复杂度
