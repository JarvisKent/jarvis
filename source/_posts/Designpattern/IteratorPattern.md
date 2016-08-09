title: 设计模式之迭代器模式
date: 2016-01-25 15:42:57
categories: [设计模式]
tags: [迭代器模式]
---
**提供一种方法顺序访问一个聚合对象中各个元素，而又不暴露该对象的内部细节。**<!--more-->

迭代器模式又称为游标（Cursor）模式。

# 迭代器模式结构图

![迭代器模式结构图](http://7xpi7i.com1.z0.glb.clouddn.com/%E8%BF%AD%E4%BB%A3%E5%99%A8%E6%A8%A1%E5%BC%8F%E7%BB%93%E6%9E%84%E5%9B%BE.jpg)

- Iterator：迭代器，负责定义访问和遍历元素的接口；
- ConcreteIterator：具体迭代器，接收一个数据对象，实现迭代器接口。
- Aggregate：容器，创建相应的迭代器对象的接口；
- ConcreteAggregate：具体容器，实现容器接口，返回ConcreteIterator的一个实例。

# 案例分析

1. 先创建一个迭代器接口
```
public interface Iterator {
	public Object first();
	public Object next();
	public Object currentItem();
	public boolean isDone();
}
```

2. 实现接口的具体迭代器
```
public class ConcreteIterator implements Iterator {

	private int currentIndex = 0;
	private Vector vector = null;

	public ConcreteIterator(final Vector vector){
		this.vector = vector;
	}
	@Override
	public Object first() {
		currentIndex = 0;
		return vector.get(currentIndex);
	}

	@Override
	public Object next() {
		currentIndex ++;
		return vector.get(currentIndex);
	}

	@Override
	public Object currentItem() {
		return vector.get(currentIndex);
	}

	@Override
	public boolean isDone() {
		if( currentIndex >= this.vector.size() -1)
			return true;
		else
			return false;
	}

}
```

3. 容器接口
```
public interface Aggregate {
	public Iterator createIterator();
}
```

4. 实现容器接口的具体容器
```
public class ConcreteAggregate implements Aggregate{

	private Vector vector = null;

	public Vector getVector() {
		return vector;
	}

	public void setVector(Vector vector) {
		this.vector = vector;
	}
	
	public ConcreteAggregate(){
		vector = new Vector<>();
		vector.add("vector 1");
		vector.add("vector 2");
	}

	@Override
	public Iterator createIterator() {
		return new ConcreteIterator(vector);
	}
	
	
}
```

5. 客户端代码
```
public static void main(String[] args) {
		//创建的迭代器
		final Aggregate agg= new ConcreteAggregate();
		final Iterator iterator = agg.createIterator();
		System.out.println(iterator.first());
		while (!iterator.isDone()) {
			System.out.println(iterator.next());
		}

		//Java自带迭代器
		List<String> list = new ArrayList<String>();
		for (int i = 0; i < 4; i++) {
			list.add("a："+i);
		}
		
		java.util.Iterator<String> b = list.iterator();
		while(b.hasNext()){
			System.out.println(b.next());
		}

	}
```

6. 运行结果
```
vector 1
vector 2
a：0
a：1
a：2
a：3
```

# 总结

迭代器模式是与集合共生共死的，一般来说，只要实现一个集合，就需要同时提供这个集合的迭代器，就像是Java中的Collection、List、Set、Map等都有自己的迭代器。如果要实现一个新的容器，也需要引入迭代器。

# 使用场合

- 访问一个聚合对象的内容而无需暴露它的内部表示；
- 支持对聚合对象的多种遍历（前->后，后->前）；
- 为遍历不同聚合结构提供一个统一接口，即多态迭代。

## 优点

- 简化了遍历方式；
- 可提供多种遍历方式；
- 增加新的聚合类和迭代器类都很方便，无须修改原有代码。符合“开放-封闭”原则

## 缺点

- 由于迭代器模式将存储数据和遍历数据的职责分离，增加新的聚合类需要对应增加新的迭代器类，类的个数成对增加，这在一定程度上增加了系统的复杂性。