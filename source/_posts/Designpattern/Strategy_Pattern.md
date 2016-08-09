title: 设计模式之策略模式
date: 2016-01-07 11:52:05
categories: [设计模式]
tags: [策略模式]
---

定义了算法家族，分别封装起来，让它们之间可以互相替换，此模式让算法的变化，不会影响到使用算法的客户。<!--more-->

以上是策略模式的概念。策略模式适用根据环境或者条件的不同而选择不同的算法或者策略来完成该功能。
![策略模式UML图](http://7xpi7i.com1.z0.glb.clouddn.com/%E7%AD%96%E7%95%A5%E6%A8%A1%E5%BC%8Fuml%E5%9B%BE.jpg)
当出现以下情况时可以使用策略模式：
1. 一个系统需要动态地在几种算法中选择一种。
2. 需要使用一个算法的不同变体。例如，你可能会定义一些反映不同的空间 /时间权衡的算法。
3. 算法使用客户不应该知道的数据。可使用策略模式以避免暴露复杂的、与算法相关的数据结构。
4. 一个类定义了多种行为 , 并且这些行为在这个类的操作中以多个条件语句的形式出现。将相关的条件分支移入它们各自的Strategy类中以代替这些条件语句。

换言之，只要在分析过程中听到需要在不同时间应用不同的业务规则，就可以考虑使用策略模式处理这种变化的可能性。

# 策略模式的使用

接下来，我们举个例子了解一下策略模式的概貌。我们去吃饭的时候可以用支付宝来进行支付。有参与支付宝支付的店一般会有搞活动，有以下几种活动：
- 打9.5折的
- 随机减
- 满10减5送5元优惠券
具体采用哪种优惠根据不同时间来选择的。

1. 我们先定义一个优惠接口，让几种优惠方式实现该接口
```
public interface Privilege {

	void Privilege();
}
```
2. 创建策略类实现优惠接口
```
public class PrivilegeA implements Privilege {

	@Override
	public void Privilege() {
		System.out.println("满10减5，送5元优惠券");
	}
}

public class PrivilegeB implements Privilege {

	@Override
	public void Privilege() {
		System.out.println("随机立减");
	}
}

public class PrivilegeC implements Privilege {

	@Override
	public void Privilege() {
		System.out.println("打9.5折");
	}
}
```
3. 接着，我们要实现一个Context类来实现对象的引用。
```
public class Context {
	Privilege contextPrivilege;
	public Context(Privilege privilege){
		this.contextPrivilege = privilege;
	}
	public void ContextInterface(){
		contextPrivilege.Privilege();
	}
}
```
4. 这样，我们就可以在客户端根据需求创建所需的优惠对象了,使用方法如下：
```
public static void main(String[] args) {
	Context context = new Context(new PrivilegeA());
	context.ContextInterface();
	context = new Context(new PrivilegeB());
	context.ContextInterface();
	context = new Context(new PrivilegeC());
	context.ContextInterface();
}
```
# 与简单工厂模式的结合

其实，我们知道，不可能所有活动一起进行的，这边一般是要根据需求做出判断。所以，我们可以考虑把之前研究的简单工厂模式也结合进来一起用。
5. 修改Context类
```
public class Context {
	Privilege p;
	public Context(String type){
		switch (type) {
			case "满10减5送5元优惠券":

				PrivilegeA a = new PrivilegeA();
				p = a;
				break;

			case "随机立减":

				PrivilegeB b = new PrivilegeB();
				p = b;
				break;

			case "打9.5折":

				PrivilegeC c = new PrivilegeC();
				p = c;
				break;

			default:
				break;
			}
	}
	public void ContextInterface(){
		p.Privilege();
	}
}
```
6. 在客户端的调用就需要做一下修改了
```
public static void main(String[] args) {
	Context context = new Context("随机立减");
	context.ContextInterface();
	context = new Context("打9.5折");
	context.ContextInterface();
	context = new Context("满10减5送5元优惠券");
	context.ContextInterface();
}
```
打印出来的效果如下：
```
满10减5，送5元优惠券
随机立减
打9.5折
```

从上面我们可以看出策略模式的优点：
- 减少各种算法类与使用算法类之间的耦合，我们可以独立改变每一个策略类而不影响其他。客户端也可以根据不同需求从不同策略中进行选择。
- 将不同策略独立封装起来，消除了条件语句。
- 方便单元测试，每个策略都有自己的类，可以通过接口单独测试。

策略模式比较明显的缺点有：
- 客户端必须知道所有策略，并且自行决定使用哪个策略。这也是它和简单工厂模式的主要区别。简单工厂模式会根据你的输入自动帮你选择创建对象，而策略则在自己输入的同时需要选择创建哪个对象。
- 当策略比较多时，会产生很多策略类。

以上是对策略模式的学习，可能理解的还不够深刻，先大致了解一下，留个印象。