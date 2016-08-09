title: 设计模式之职责链模式
date: 2016-01-24 18:47:39
categories: [设计模式]
tags: [职责链模式]
---
**使多个对象都有机会处理请求，从而避免请求的发送者和接收者之间的耦合关系。将这个对象连成一条链，并沿着这条链传递该请求，直到有一个对象处理它为止。**<!--more-->

职责链模式，初看起来就像是推卸责任的模式，一件事情你请求A帮忙，A推给了B，B推给了C，直至找到了D，D可能会帮忙，也可能直接回绝了。至于到底谁解决呢，这就不用咱们管了。

# 职责链模式类图解析

![职责链模式结构图](http://7xpi7i.com1.z0.glb.clouddn.com/%E8%81%8C%E8%B4%A3%E9%93%BE%E6%A8%A1%E5%BC%8F%E7%BB%93%E6%9E%84%E5%9B%BE.png)

结构图很简单：
- Handler：是抽象处理者，定义了一个处理请求的接口。当然对于链子的不同实现，也可以在这个角色中实现后继链。

- Concrete Handler：具体处理者，实现了抽象处理者的接口，并负责它所负责的请求。如果不能处理则访问它的后继者。

# 案例解析

公司请假，通常都会有一个流程。当你请假不超过2天时，可直接跟经理请假；超过两天不超过5天时，需向总监请示，超过5天后，则需要向总经理申请了。

我们来看看代码的实现：

1. 创建一个Request对象，包含请求内容和天数
```
public class Request {
	String Type;
	int Number;
	public String getType() {
		return Type;
	}
	public void setType(String type) {
		Type = type;
	}
	public int getNumber() {
		return Number;
	}
	public void setNumber(int number) {
		Number = number;
	}
	
}
```
2. 创建一个抽象处理者Handler，作为管理者，我们创建一个Manager类
```
public abstract class Manager {

	protected String name;
	/**
	 * 上级
	 */
	protected Manager superior;

	public Manager(String name){
		this.name = name;
	}

	public void SetSuperior(Manager superior){
		this.superior = superior;
	}
	/**
	 * 请假申请
	 */
	abstract public void RequestApplications(Request request);
}
```

3. 分别创建经理、总监总经理的具体处理对象类
```
/**
 * 经理，请假不超过两天直接批准
 */
public class CommonManager extends Manager {

	public CommonManager(String name) {
		super(name);
	}

	public void RequestApplications(Request request) {
		if (request.Type == "请假" && request.Number <=2) {
			System.out.println("向"+name+"请假"+request.Number +"天，批注");
		}else{
			if(superior != null){
				System.out.println("请假超过2天，经理需向"+superior.name+"请示。");
				superior.RequestApplications(request);
			}
		}
	}
}

/**
 * 总监，请假超过2天不超过5天可批准
 */
public class MajorDomo extends Manager {

	public MajorDomo(String name) {
		super(name);
	}

	public void RequestApplications(Request request) {
		if(request.Type == "请假" && request.Number > 2 && request.Number <=5){
			System.out.println("向"+name+"请假 :"+request.Number+"批准");
		}else{
			if(superior != null){
				System.out.println("请假天数超过5天，总监需向"+superior.name+"请示");
				superior.RequestApplications(request);
			}
		}
	}

}

/**
 * 总经理，请假超过5天不超过10天都批准
 */
public class GeneralManager extends Manager {

	public GeneralManager(String name) {
		super(name);
	}

	public void RequestApplications(Request request) {
		if(request.Type=="请假" && request.Number >5 && request.Number <= 10){
			System.out.println("向"+name+"请假"+request.Number +"天，批准");
		}else{
			System.out.println("请假过多，不批准");
		}
	}

}
```
4. 在客户端申请代码
```
public static void main(String[] args) {
	//创建三个领导
	CommonManager jingli = new CommonManager("王经理");
	MajorDomo major = new MajorDomo("张总监");
	GeneralManager zongjingli = new GeneralManager("郑总经理");
	//设置经理上一级是总监，总监上一级是总经理
	jingli.SetSuperior(major);
	major.SetSuperior(zongjingli);
	//创建一个7天假期的请求开始申请
	Request r =new Request();
	r.setNumber(7);
	r.setType("请假");
	jingli.RequestApplications(r);	
}
```

5. 代码运行结果
```
请假超过2天，经理需向张总监请示。
请假天数超过5天，总监需向郑总经理请示
向郑总经理请假7天，批准

```

# 总结

职责链模式其实就是当客户提交一个请求后，在客户端创建一系列可能处理的对象串联起来，让请求沿着串联起来的链子传递直至有一个处理对象处理它。这就使得发送请求的客户和接收者都没有对方明确的信息，且各个处理对象也不知道链的结构。简化了对象相互间的连接，仅需保持一个指向后继者的引用，不需要保持所有候选处理对象的引用。增强了指派职责的灵活性，可以随时地增加或修改处理一个请求的结构。

## 适用范围

- 有多个对象可以处理同一个请求，具体哪个对象处理该请求由运行时刻自动确定。
- 在不明确接收者的情况下，向多个对象中的一个提交请求。
- 可动态指定一组对象处理请求。

## 优点

- 解耦了请求者和处理者直接复杂关系，只需把请求从第一个处理者开始传递即可。
- 可以根据需要创建任意顺序的处理链子。可灵活拆分重组。

## 缺点

- 每次处理一个请求都需要从责任链的开头开始遍历，效率低。
- 不能保证请求一定被接收处理。