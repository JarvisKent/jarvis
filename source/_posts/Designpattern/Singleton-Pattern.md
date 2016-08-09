title: 设计模式之单体模式
date: 2016-01-18 09:21:41
categories: [设计模式]
tags: [单体模式]
---
**保证一个类仅有一个实例，并提供一个访问它的全局访问点**<!--more-->
# 单体模式

单例模式因为singleton类封装它的唯一实例，这样它可以严格地控制客户怎样访问它以及何时访问它，简单地说就是对唯一实例的受控访问。

单体模式的实现要提供给一个私有构造器，避免多个单体对象创建，这意味着它不能有子类，声明单体类为final是个好主意。使用静态域来维护实例，即使用保存唯一实例的static变量，其类型就是单例类型本身。使用final使其不能被重载。

# 单体模式的几种写法

## 懒汉
懒汉模式：在第一次被引用时将自己实例化。
```
public class Singleton{
	/**
     * 此时静态变量不能声明为final，因为需要在工厂方法中对它进行实例化
     */
	private static Singleton instance;
	/**
     * 私有的默认构造子,详见末尾注释
     */
	private Singleton(){}
	/**
     * synchronized关键字解决多个线程的同步问题
     */
	public static synchronized Singleton getInstance(){
		if(instance == null){
			instance = new Singleton();
		}
		return instance;
	}
}
```

## 饿汉
饿汉模式：在类被加载时实例化。
```
public class Singleton{
	private static final Singleton instance = new Singleton();
	/**
     * 私有的默认构造子
     */
	private Singleton(){}
	/**
     * 获取唯一类实例
     * 
     * @return
     */
	public static Singleton getInstance(){
		return instance;
	}
}
```
## 双重锁
```
public class Singleton{
	private static Singleton instance = null;
	private Singleton(){}
	public static Singleton getInstance(){
		if(instance == null){
			synchronized(Singleton.class){
				if(instance == null){
					instance = new Singleton();
				}
			}
		}
		return instance;
	}
}
```
双重锁的使用是在多线程里，当两个线程一起进入到getInstance中，第一个线程会通过锁进行实例化，第二个线程被锁挡住，当第一个线程通过，第二个线程进入时，如果没有进行判断，则会再实例化一次。


***在JAVA中，所有类都有构造方法，不编码则系统默认生成空的构造方法，若有显示定义的构造方法，默认的构造方法就会失效。***