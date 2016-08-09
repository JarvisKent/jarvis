title: 设计模式之组合模式
date: 2016-01-21 14:17:52
categories: [设计模式]
tags: [组合模式]
---
**将对象组合成树形结构以表示‘部分-整体’的层次结构。组合模式使得用户对单个对象和组合对象的使用具有一致性。**<!--more-->

# 组合模式使用场景
提到组合模式，一般都是跟树形结构图挂钩，使用场景一般是：
1. 用于对象的部分-整体层次结构，如树形菜单、文件夹菜单、部门组织架构图等；
2. 对用户隐藏组合对象与单个对象的不同，使得用户统一地使用组合结构中的所有对象。

当发现需求中是体现部分与整体层次结构时，以及你希望用户可以忽略组合对象与单个对象的不同，统一地使用组合结构中的所有对象时，就应该考虑组合模式了。

# 结构图

![组合模式结构图](http://7xpi7i.com1.z0.glb.clouddn.com/%E7%BB%84%E5%90%88%E6%A8%A1%E5%BC%8F%E7%BB%93%E6%9E%84%E5%9B%BE.jpg)

- Component：组合中对象声明接口，在适当情况下，实现所有类共有接口的默认行为。声明一个接口用于访问和管理Component子部件。
- Leaf：组合中表示叶子结点对象，叶子结点没有子结点。
- Composite：定义有枝结点行为，用于存储子部件，在Component接口中实现与子部件相关操作，如add和remove等。

# 案例分析

通过一个简单的案例，来看看组合模式的概貌。

1. 创建一个抽象类Component，实现所有类共有的默认行为：
```
public abstract class Component {

	String name;
	public Component(String s){
		this.name = s;
	}
	public abstract void add(Component c);
	public abstract void remove(Component c);
	public abstract void foreach();
}
```

2. 创建一个Composite存储子部件：
```
/**
 * 组合类
 */
public class Composite extends Component{
	private List<Component> child = new ArrayList<Component>();
	public Composite(String s) {
		super(s);
	}

	@Override
	public void add(Component c) {
		child.add(c);
	}

	@Override
	public void remove(Component c) {
		child.remove(c);	
	}

	@Override
	public void foreach() {
		System.out.println("节点名: \t"+name);
		for(Component c: child)
			c.foreach();
	}

}
```

3. 创建叶子结点：
```
public class Leaf extends Component{

	public Leaf(String s) {
		super(s);
	}

	@Override
	public void add(Component c) {
		
	}

	@Override
	public void remove(Component c) {
		
	}

	@Override
	public void foreach() {
		System.out.println("leaf name ----->"+this.name);
	}

}
```

4. 客户端的使用。
```
public static void main(String[] args) {
		
		Component component = new Composite("根节点");
		
		Component child = new Composite("一级子节点child1");
		Component child_1 = new Leaf("一级节点child1的子节点1");
		Component child_2 = new Leaf("一级节点child1的子节点2");
		child.add(child_1);
		child.add(child_2);
		
		Component child2 = new Composite("一级子节点child2");
		component.add(child);
		component.add(child2);
		component.foreach();
	}
```

5. 运行效果
```
节点名: 	根节点
节点名: 	一级子节点child1
leaf name ----->一级节点child1的子节点1
leaf name ----->一级节点child1的子节点2
节点名: 	一级子节点child2
```

***以上，为组合模式的学习笔记。只是大致懂了组合模式的概念，不是很明白在实际应用中的使用，先记录下来，混个脸熟。***