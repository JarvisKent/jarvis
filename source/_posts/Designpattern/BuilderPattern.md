title: 设计模式之建造者模式
date: 2016-01-20 14:32:53
categories: [设计模式]
tags: [建造者模式]
---
**将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。**<!--more-->

# 结构图解析

可以理解为一个产品的生产步骤是固定，我们把它的步骤抽离出来，可以根据自己的需要改变任意一个步骤的具体内容，使得在同样的生产步骤中可以生产出不同的产品。
![建造者模式结构图](http://7xpi7i.com1.z0.glb.clouddn.com/%E5%BB%BA%E9%80%A0%E8%80%85%E6%A8%A1%E5%BC%8F%E7%BB%93%E6%9E%84%E5%9B%BE.jpg)

- Builder：是一个抽象接口，提供一个产品各个组件的成分的制作，规定了要实现的产品的哪些部分需要创建，不涉及具体组件的创建过程。
- ConcreteBuilder：实现Builder接口，针对不同需求，具体实现各个组件的创建，**创建过程完成后提供产品的实例**。
- Director：调用ConcreteBuilder来创建产品的各个部分，在Director中不涉及产品的具体信息，只负责保证产品的完整创建或按某个顺序创建。
- Product：要构建的产品。

# 案例分析

接下来，我们来讲一个神造人的故事YY版。神在初次造人的时候，只想造两种人（Product），一种是胖子，一种是巨人。建造的人都有头、身躯、手和脚（Builder）。为了方便，神制造了两个模板（ConcreteBuilder），一个只要材料扔进去就会出来胖子，一个是材料扔进去就会出来巨人。

接下来，我们来看看具体实现。
1. 场景Human类，即产品类：
```
public class Human {
	private String head;
	private String feets;
	private String hands;
	private String body;
	public String getHead() {
		return head;
	}
	public void setHead(String head) {
		this.head = head;
	}
	public String getFeets() {
		return feets;
	}
	public void setFeets(String feets) {
		this.feets = feets;
	}
	public String getHands() {
		return hands;
	}
	public void setHands(String hands) {
		this.hands = hands;
	}
	public String getBody() {
		return body;
	}
	public void setBody(String body) {
		this.body = body;
	}
	@Override
	public String toString() {
		return "创造的形象 ["+head +" ,"+  body+" ," + hands
				+" ,"+  feets + "]";
	}
	
}
```

2.创建一个Builder，即设定人必须有头、身躯、手脚。
```
public interface Builder {

	void buildHead();
	void buildBody();
	void buildHands();
	void buildFeets();
	/**
	 * 用于返回一个人的对象
	 * @return Human
	 */
	Human returnResult();
}
```

3. 制作两种人的模板ConcreteBuilder。
```
/**
 * 这是个胖子的模板
 */
public class FatmanBuilder implements Builder {
	private Human fatman = new Human();
	@Override
	public void buildHead() {
		fatman.setHead("大头");
	}

	@Override
	public void buildBody() {
		fatman.setBody("肥胖的身躯");
	}

	@Override
	public void buildHands() {
		fatman.setHands("粗短的手臂");
	}

	@Override
	public void buildFeets() {
		fatman.setFeets("短小的腿");
	}

	@Override
	public Human returnResult() {
		return fatman;
	}
}

/**
 * 这是个高个子的模板
 */
public class TallmanBuilder implements Builder{
	private Human tallMan = new Human();
	@Override
	public void buildHead() {
		tallMan.setHead("小头");
	}

	@Override
	public void buildBody() {
		tallMan.setBody("瘦瘦的身躯");
	}

	@Override
	public void buildHands() {
		tallMan.setHands("大大的手");
	}

	@Override
	public void buildFeets() {
		tallMan.setFeets("大尺寸的脚");
	}
	@Override
	public Human returnResult() {
		return tallMan;
	}

}
```
4. 有一个管理者（Director）来主管造人的步骤，神（Client）只要负责决定造什么人就好了。
```
public class Director {

	private Builder builder;
	/**
	 * 构造方法，传入建造器对象
	 * @param builder
	 */
	public Director(Builder builder){
		this.builder = builder;
	}
	/**
	 * 产品构造方法，负责调用各个零件建造方法
	 */
	public void construct(){
		builder.buildHead();
		builder.buildBody();
		builder.buildHands();
		builder.buildFeets();
	}
}
```
5. 接下来，我们看看神(Client)的具体工作。
```
	public static void main(String[] args) {
		//决定要构造的人的样子
		Builder  builder = new FatmanBuilder();
		//告诉管理者
		Director director = new Director(builder);
		//管理者开始制造
		director.construct();
		//生成制造的人
		Human human = builder.returnResult();
		//人的形象展示
		System.out.println(human.toString());
	}	
```
6. 最终结果
```
创造的形象 [大头 ,肥胖的身躯 ,粗短的手臂 ,短小的腿]
```

# 总结

## 适用范围

- 隔离一个复杂的对象创建和使用，并使得相同的创建过程中可以创建出不同的产品；
- 一个复杂对象的部件相对稳定，不会发生变化时；
- 当创建复杂对象的算法应该独立于该对象的组成部分以及它们的装配方式时。 

## 优点

- 客户端只要指定创建的产品的类型就能够得到它们，不需要知道具体的创建过程和细节；
- 构建代码和表示相分离，如果要改变一个产品的某一内部表示，只需再定义一个新的具体建造者即可；
- 建造过程由Director来控制，建造的细节由抽象类来控制，对于实现建造细节的具体类来说，不会遗漏某个步骤。

## 缺点

- 产品的每一个组件都被定义在Builder中，每增加一个新的产品细节都需要修改Builder，违背了“开放封闭原则”。
- 建造者模式创建的产品一般具有较多共同点，若是产品差异性过大，则不适用此模式，因此，建造者模式的使用范围也有一定限制。
- 