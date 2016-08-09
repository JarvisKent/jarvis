title: 设计模式之抽象工厂模式
date: 2016-01-13 10:31:33
categories: [设计模式]
tags: [抽象工厂模式]
---
**提供一个创建一系列相关或者相互依赖对象的接口，而无需指定它们具体的类。**<!--more-->

# 抽象工厂和工厂方法的区别

抽象工厂模式是建立在工厂方法模式的基础上的。
- 工厂方法针对的是一个产品等级机构，而抽象工厂模式则是面向多个产品等级结构。
- 工厂方法模式是创建一个抽象产品，派生出多个具体产品；而抽象工厂则是创建多个抽象产品，每个抽象产品可派生出多个具体产品；
- 工厂方法中的抽象工厂可以派生出多个具体工厂，每个具体工厂只能派生出一个具体产品实例；而抽象工厂模式中的抽象工厂可以派生出多个具体工厂，每个具体工厂可以创建多个具体产品实例。

# 抽象工厂模式结构图

![抽象工厂类图](http://7xpi7i.com1.z0.glb.clouddn.com/%E6%8A%BD%E8%B1%A1%E5%B7%A5%E5%8E%82%E6%A8%A1%E5%BC%8F%E7%B1%BB%E5%9B%BE.jpg)

和工厂方法模式对比，可以看出，就是工厂方法的进一步拓展。每多一个产品，就会增加它的抽象产品类，及具体工厂类，并且在抽象工厂类中添加新增产品的方法。

# 案例分析

在一个项目中，我们需要把数据插入到数据库，需要插入的数据分别是用户信息和部门信息分别对应于用户表和部门表，而数据库会根据要求在Access和SQLServer之间做选择。

1. 我们先创建用户信息操作接口和部门信息操作接口，及2个抽象产品
```
/**
 * 用户信息进行增删改查操作
 */
public abstract class IUser {

	public abstract void insert();
	public abstract void update();
	public abstract void delete();
	public abstract void search();
}

/**
 * 部门信息进行增删改查操作
 */
public abstract class IDepartment {

	public abstract void insert();
	public abstract void update();
	public abstract void delete();
	public abstract void search();
}
```
2. 创建两个产品在Access数据库和SQLServer数据库中的具体操作，即具体产品的创建。
先创建在Access中的部门和用户操作
```
/**
 *Access中用户信息操作
 */
public class AccessForIUser extends IUser {
	@Override
	public void insert() {
		System.out.println("Access  插入用户数据");
	}
	@Override
	public void update() {
		System.out.println("Access  更新用户数据");
	}
	@Override
	public void delete() {
		System.out.println("Access  删除用户数据");
	}
	@Override
	public void search() {
		System.out.println("Access  查询用户数据");
	}
}

/**
 *Access中部门信息操作
 */
public class AccessForIDepartment extends IDepartment {
	@Override
	public void insert() {
		System.out.println("插入一条部门数据到Access");
	}
	@Override
	public void update() {
		System.out.println("更新一条部门数据到Access");
	}
	@Override
	public void delete() {
		System.out.println("删除一条部门数据到Access");
	}
	@Override
	public void search() {
		System.out.println("查询一条部门数据到Access");
	}
}
```
再创建SQLServer中的用户和部门信息操作
```
/**
 *SQLServer中部门信息操作
 */
public class SQLServerForUDepartment extends IDepartment {
	@Override
	public void insert() {
		System.out.println("插入一条部门数据到sqlserver");
	}
	@Override
	public void update() {
		System.out.println("更新一条部门数据到sqlserver");
	}
	@Override
	public void delete() {
		System.out.println("删除一条部门数据到sqlserver");
	}
	@Override
	public void search() {
		System.out.println("查询一条部门数据到sqlserver");
	}
}

/**
 *SQLServer中用户信息操作
 */
public class SQLServerForIUser extends IUser{
	@Override
	public void insert() {
		System.out.println("SQLServer  插入数据");
	}
	@Override
	public void update() {
		System.out.println("SQLServer  更新数据");
	}
	@Override
	public void delete() {
		System.out.println("SQLServer  删除数据");
	}
	@Override
	public void search() {
		System.out.println("SQLServer  查询数据");
	}
}
```

3. 接着，创建抽象工厂类，因为要对用户和部门进行操作，即要对两个产品进行操作，所有工厂类中需要创建两个产品的实例方法。
```
/**
 *抽象工厂类，要操作的产品是用户表和部门表，所以要生成的方法是创建用户操作实例和部门操作*实例。
 */
public abstract class DBFactory {

	public abstract IUser CreateUser();
	public abstract IDepartment CreateDepartment();
}
```
4. 接着就是创建具体的工厂了，分别是SQLServerFactory和AccessFactory两个具体工厂
```
public class AccessFactory extends DBFactory{
	@Override
	public IUser CreateUser() {
		return new AccessForIUser();
	}
	@Override
	public IDepartment CreateDepartment() {
		return new AccessForIDepartment();
	}
}

public class SQLServerFactory extends DBFactory{
	@Override
	public IUser CreateUser() {
		return new SQLServerForIUser();
	}
	@Override
	public IDepartment CreateDepartment() {
		return new SQLServerForUDepartment();
	}
}
```

5. 接下来就是在客户端的使用了
```
public static void main(String[] args) {
//		DBFactory factory = new AccessFactory();
		DBFactory factory = new SQLServerFactory();
		
		IUser user = factory.CreateUser();
		user.insert();
		
		IDepartment department = factory.CreateDepartment();
		department.insert();
	}
```
这样，当我们切换数据库时，只需把
```
DBFactory factory = new SQLServerFactory();
```
切换成
```
DBFactory factory = new AccessFactory();
```
就可以了，运行结果为：
```
SQLServer  插入数据
插入一条部门数据到sqlserver
```

# 抽象工厂解析

抽象工厂的好处在于当存在多个产品时易于产品的切换，具体工厂类在一个应用中只需初始化一次，使得改变一个应用的具体工厂类变得容易，只需改变具体工厂类即可切换产品的配置。

而缺点在于产品族拓展比较麻烦，要对抽象工厂进行修改增加创建新产品的方法，然后要创建或修改对应的具体实现类，这样又违反了"**开放-封闭原则**"。

在拓展产品族上麻烦又违反开闭原则，不过在拓展某个产品的等级上却很方便，且符合开闭原则。比如，再增加一个DB2的数据库，我们只需要创建相应的具体产品操作及具体工厂类即可。

