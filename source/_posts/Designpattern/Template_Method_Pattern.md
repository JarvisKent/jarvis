title: 设计模式之模板方法模式
date: 2016-01-14 10:25:14
categories: [设计模式]
tags: [模板方法模式]
---
**定义一个操作中的算法的骨架，而将一些步骤延迟到子类中，模板方法使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。**<!--more-->

模板方法模式是所有模式中最为常见的几个模式之一，是基于继承的代码复用的基本技术。通过把不变行为搬移到超类，去除子类中的重复代码来体现它的优势。

# 模板方法结构图

![模板方法模式结构图](http://7xpi7i.com1.z0.glb.clouddn.com/%E6%A8%A1%E6%9D%BF%E6%96%B9%E6%B3%95%E7%BB%93%E6%9E%84%E5%9B%BE.jpg)

在模板方法中有两个角色：抽象模板和具体模板。
- 抽象模板（Abstract Template）：定义了一个或多个抽象操作，以便让子类实现。在抽象模版中定义并实现一个模板方法。这个模板方法一般是一个具体方法，它给出了一个顶级逻辑的骨架，而逻辑的组成步骤在相应的抽象操作中，推迟到了子类实现。顶级逻辑也有可能调用一些具体方法。
- 具体模板（Concrete Template）：实现父类所定义的一个或多个抽象方法，它是一个顶级逻辑的组成步骤。

# 需要知道的一些概念

在模板方法中的方法一般分为两类：模板方法和基本方法。

1. **模板方法**：定义在抽象类中的，把基本操作方法组合在一起形成一个总算法或者总行为的方法。一个抽象类可以有任意多个模板方法，每个模板方法可以调用任意多个具体方法。**为了防止子类改变算法的实现步骤，一般会将模板方法声明为final**

2. **基本方法**：基本方法又分为三种：抽象方法、具体方法和钩子方法。

- **抽象方法**（Abstract Method）：由抽象类声明，由具体子类实现。
- **具体方法**（Concrete Method）：由抽象类声明并实现，子类不实现或置换。
- **钩子方法**（Hook Method）：由抽象类声明并实现，而子类会加以拓展。通常抽象类给出的实现是一个空实现，作为方法的默认实现。这种空的钩子方法叫做“Do Nothing Hook”。

# 案例分析

假设有个存款系统，支持两种不同账号存款。即货币市场和定期存款账号。这两种账号的存款利息是不同的，在计算一个存户利息时，需要进行账号类型区分。

这个系统的行为是计算出利息，模板上要有一个基本方法给出账号类别获取存款金额、一个基本方法给出利率。

1. 首先，我们创建模板抽象类
```
public abstract class AbstractTemplate {

	public final double templateMethod(){
		
		double interestRate = doCalculateInterestRate();
		String accountType = doCalculateAccountType();
		double amount = calculateAmount(accountType);
		
		return amount * interestRate;
	}

	private double calculateAmount(String accountType) {
		//根据账户类型，进行逻辑判断 ，返回具体金额，假定都有8000
		return 8000;
	}
	
	/**
	 * @return 账户类型
	 */
	protected String doCalculateAccountType() {
		return null;
	}
	
	/**
	 * @return 利率
	 */
	protected double doCalculateInterestRate() {
		return 0;
	}
}
```
2. 定义具体模板方法
```
/**
 * 定期存款，利率0.06
 */
public class CDAccoount extends AbstractTemplate {

	@Override
	protected String doCalculateAccountType() {
		return "Certificate of Deposite";
	}
	
	@Override
	protected double doCalculateInterestRate() {
		return 0.06;
	}
}

/**
 * 货币市场，利率0.045
 */
public class MoneyMarketAccount extends AbstractTemplate {
	
	@Override
	protected String doCalculateAccountType() {
		return "Money Market";
	}
	
	@Override
	protected double doCalculateInterestRate() {
		return 0.045;
	}
}
```
3. 在客户端的使用
```
public static void main(String[] args) {
		AbstractTemplate a = new CDAccoount();
		System.out.println("定期存款利息为："+a.templateMethod());
		AbstractTemplate b = new MoneyMarketAccount();
		System.out.println("货币市场利息为："+b.templateMethod());
	}
```
4. 运行结果为：
```
定期存款利息为：480.0
货币市场利息为：360.0
```

# 总结

- 优点

1. 模板方法模式定义了一组算法，将具体实现交给了子类，通过父类调用其子类的操作，通过子类的拓展增加行为，符合“开放-封闭”原则。

2. 各子类中的公共行为被集中到了一个父类中，避免了代码重复。

- 缺点

1. 因为每个实现都需要一个子类来实现，而会导致类的数量增加，使系统变得庞大。