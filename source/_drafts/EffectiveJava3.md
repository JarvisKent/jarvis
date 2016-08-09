title: (3)、类和接口
date: 2016-03-15 09:21:18
categories: [Effective Java笔记]
tags: [类和接口]
---

# 使类和成员的可访问性最小化

**尽可能地使每个类或者成员不被外界访问。**
访问级别有四种：
- 私有(private)：只有该类内部可以访问；
- 包级私有(package-private)：缺省访问级别；没有修饰符，包内部任何类都可以访问。
- 受保护(protected)：声明该成员的类的子类可以访问这个成员，声明该成员的包内部任何类也可以访问这个成员；
- 公有(public)：任何地方都可以访问。

# 在公有类中使用访问方法而非公有域

**如果类可以在它所在的包的外部进行访问，就提供访问方法。**
公有类不应该直接暴露数据域。如
```
public class Point{
	public double x;
	public double y;
}
```
应该用公有访问方法的类代替
```
class Point{
	private double x;
	private double y;
	public Point(double x, double y){
		this.x = x;
		this.y = y;
	}
	public double getX(){return x;}
	public double getY(){return y;}
	public void setX(double x){this.x = x;}
	public void setY(double y){this.y = y;}
}
```

# 使可变性最小化

- 不要提供任何会修改对象状态的方法
- 保证类不会被拓展。一般做法是使这个类成为final的，当然还有其他做法。
- 使所有的域都是final的
- 使所有的域都成为私有的
- 确保对于任何可变组件的可斥访问

# 复合优先于继承
# 要么为继承而设计，要么提供文档说明，要么就禁止继承
# 接口优于抽象类
# 接口只用于定义类型
# 类层次优于标签类
# 用函数对象表示策略
# 有限考虑静态成员类