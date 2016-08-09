title: 设计模式之观察者模式
date: 2016-01-25 10:11:12
categories: [设计模式]
tags: [观察者模式]
---
**定义了一种一对多的依赖关系，让多个观察者对象同时监听某一个主题对象。这个主题对象在状态发生变化时，会通知所有观察者对象，使它们能够自动更新自己。**<!--more-->

观察者模式又叫发布-订阅模式。当客户端订阅了某一个对象时，这个对象的状态有发生改变的话就会通知有订阅它的客户端。

# 观察者模式结构图

![观察者模式结构图](http://7xpi7i.com1.z0.glb.clouddn.com/%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F%E7%BB%93%E6%9E%84%E5%9B%BE.jpg)

- Subject：抽象主题角色，为所有对观察者对象的引用保存在一个集合里，每个抽象主题角色都可以有任意数量的观察者。抽象主题提供了一个接口，可以增、减观察者。一般用抽象类或接口实现。
- ConcreteSubject：具体主题角色，在具体主体内部状态改变时，给所有登记过的观察者发出通知。通常用一个子类实现。
- Observer：抽象观察者角色，为所有具体的观察者定义一个接口，在得到通知时更新自己。
- ConcreteObserver：具体观察者角色，实现抽象观察者角色所要求的接口，以便使自己的状态与主题状态相协调。也可以根据需要，保存一个指向具体角色的引用。

# 案例分析

漂亮的事物总是会让人觉得欣喜。当你总在大街上，看到一个美女迎面走来，总会忍不住瞥一眼，可能美女的某个动作，会让你觉得惊喜，原来这个动作可以这么优雅、好看。在这里面，你就是观察者，而美女就是被观察者，美女的不经意动作会使你的心境发生改变。

1. 首先定义一个抽象观察者接口，需要提供一个更新的方法。
```
/**
 * 抽象观察者
 */
public interface Watcher {

	public void update(String str);
}
```

2. 具体观察者。
```
public class ConcreteWatcher implements Watcher {

	@Override
	public void update(String str) {
		//实现当被观察者状态发生变化时，观察者要进行更新的逻辑
		System.out.println(str);
	}

}

```

3. 被观察者接口及具体被观察者
```
/**
 * 抽象主题，被观察者。
 */
public interface Watched {

	public void addWatcher(Watcher watcher);
	public void removerWatcher(Watcher watcher);
	public void notifyWatchers(String str);
}

public class ConcreteWatched implements Watched {

	private List<Watcher> list = new ArrayList<Watcher>();
	@Override
	public void addWatcher(Watcher watcher) {
		list.add(watcher);
	}

	@Override
	public void removerWatcher(Watcher watcher) {
		list.remove(watcher);
	}
	/**
	 *状态发生改变时
	 */
	@Override
	public void notifyWatchers(String str) {
		 // 自动调用实际上是主题进行调用的
        for (Watcher watcher : list)
        {
            watcher.update(str);
        }
	}

}
```

4. 客户端中的绑定。
```
public static void main(String[] args) {
	Watched girl = new ConcreteWatched();
	
	Watcher w1 = new ConcreteWatcher();
	Watcher w2 = new ConcreteWatcher();
	Watcher w3 = new ConcreteWatcher();
		
	girl.addWatcher(w1);
	girl.addWatcher(w2);
	girl.addWatcher(w3);
		
	girl.notifyWatchers("happy.");
}
```
5. 实现效果。
```
happy.
happy.
happy.
```

# 总结

将一个系统分割成一个一些类相互协作的类有一个不好的副作用，那就是需要维护相关对象间的一致性。我们不希望为了维持一致性而使各类紧密耦合，这样会给维护、扩展和重用都带来不便。观察者就是解决这类的耦合关系的。

## 使用场景

- 当一个对象改变时需要改变其他对象，而且不知道具体有多少个对象要一起改变。

- 一个抽象模型有两个方面，当其中一个方面依赖另一个方面，用观察者模式可以将这两者封装在独立的对象中，使它们各自独立地改变和复用。

- 一个对象必须通知其他对象，而并不知道这些对象是谁。需要在系统中创建一个触发链，A对象的行为将影响B对象，B对象的行为将影响C对象……，可以使用观察者模式创建一种链式触发机制。

## 优点

- 两个对象（观察者和被观察者）之间松耦合，依然可以交互，且不太清楚彼此的细节。每一个具体观察者都符合一个抽象观察者的接口。主题并不认识任何一个具体的观察者，它只知道他们都有一个共同的接口。

- 支持“广播通信”，主题会向所有观察者发出通知。

- 符合“开放-封闭”原则。

## 缺点

- 观察者很多时，通知所有观察者需花费不少时间。

- 观察者和被观察者之间如果有循环依赖的话（其实，依赖关系也并未完全解除），被观察者会触发它们之间循环调用，可能导致系统崩溃。

-  观察者模式没有相应的机制让观察者知道所观察的目标对象是怎么发生变化的，而仅仅只是知道观察目标发生了变化。