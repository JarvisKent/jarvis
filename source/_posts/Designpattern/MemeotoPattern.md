title: 设计模式之备忘录模式
date: 2016-01-25 11:38:11
categories: [设计模式]
tags: [备忘录模式]
---
**在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可将该对象恢复到原先保存的状态了。**<!--more-->

备忘录模式又称为快照模式。最典型的例子应该就是游戏的存档功能了，打没把握过的Boss之前先保存一些，以免重头再来。

# 备忘录模式结构图

![备忘录模式结构图](http://7xpi7i.com1.z0.glb.clouddn.com/%E5%A4%87%E5%BF%98%E5%BD%95%E6%A8%A1%E5%BC%8F%E7%BB%93%E6%9E%84%E5%9B%BE.jpg)

- Originator：发起人，负责创建一个备忘录Memento，用以记录当前时刻自身的内部状态，并可使用备忘录恢复内部状态，Originator可以根据需要决定Memento存储自己的哪些内部状态。
- Memento：备忘录，负责存储Originator对象的内部状态，并可以防止Originator以外的其他对象访问备忘录。备忘录有两个接口：Caretaker只能看到备忘录的窄接口，他只能将备忘录传递给其他对象。Originator却可看到备忘录的宽接口，允许它访问返回到先前状态所需要的所有数据。
- Caretaker：管理者，负责备忘录Memento，不能对Memento的内容进行访问或操作。

# 案例分析

1. 创建一个角色规则类，对应于Originator，除了包含自身属性外还有创建备忘和恢复备忘的方法。
```
public class PlayRole {
	private int vitality;//生命力
	private int aggressivity;//攻击力
	private int defencivity;//防御力
	public PlayRole(int vatality,int aggressivity,int defencivity){
		this.vitality = vatality;
		this.aggressivity = aggressivity;
		this.defencivity = defencivity;
	}
	public PlayRole(){}
	public int getVitality() {
		return vitality;
	}
	public void setVitality(int vitality) {
		this.vitality = vitality;
	}
	public int getAggressivity() {
		return aggressivity;
	}
	public void setAggressivity(int aggressivity) {
		this.aggressivity = aggressivity;
	}
	public int getDefencivity() {
		return defencivity;
	}
	public void setDefencivity(int defencivity) {
		this.defencivity = defencivity;
	}

	public RoleMemento createMemento(){
		RoleMemento memento = new RoleMemento();
		memento.setAggressivity(aggressivity);
		memento.setDefencivity(defencivity);
		memento.setVitality(vitality);
		return memento;
	}
	public void setMemento(RoleMemento memento){
		this.aggressivity = memento.getAggressivity();
		this.defencivity = memento.getDefencivity();
		this.vitality = memento.getVitality();
	}
	public void showState(){
		System.out.println("攻击力："+this.aggressivity+"| 防御力："+this.defencivity+"| 生命力："+this.vitality);
	}
}
```

2. 创建一个备忘录Memento，保存要进行保存的数据，即攻击力、防御力和生命力。
```
public class RoleMemento {
private int vitality;
private int aggressivity;
private int defencivity;
public int getVitality() {
	return vitality;
}
public void setVitality(int vitality) {
	this.vitality = vitality;
}
public int getAggressivity() {
	return aggressivity;
}
public void setAggressivity(int aggressivity) {
	this.aggressivity = aggressivity;
}
public int getDefencivity() {
	return defencivity;
}
public void setDefencivity(int defencivity) {
	this.defencivity = defencivity;
}

}
```
3. 创建一个管理者Caretaker，主要负责Memento，不访问Memento内部。
```
public class Caretaker {

	RoleMemento memento;
	public RoleMemento getMemento(){
		return memento;
	}
	public void setMemento(RoleMemento memento){
		this.memento = memento;
	}
}
```

4. 完成后，来看看在客户端如何使用：
```
public static void main(String[] args) {
	//新角色
	PlayRole role = new PlayRole(100, 100, 100);
	//管理者
	Caretaker taker = new Caretaker();
	//初始状态
	System.out.println("游戏刚开始，角色状态：");
	role .showState();

	System.out.println("保存游戏，准备进入Boss战");
	taker.setMemento(role.createMemento());
	//进入一场战斗ing
	role.setAggressivity(30);
	role.setDefencivity(40);
	role.setVitality(10);
	//大战后角色能力
	System.out.println("Boss战后，角色能力：");
	role.showState();

	System.out.println("使用存档");
	role.setMemento(taker.getMemento());
	role.showState();
}
```

5. 运行结果如下:
```
游戏刚开始，角色状态：
攻击力：100| 防御力：100| 生命力：100
保存游戏，准备进入Boss战
Boss战后，角色能力：
攻击力：30| 防御力：40| 生命力：10
使用存档
攻击力：100| 防御力：100| 生命力：100
```

即保存了初始状态下的角色状态，可随时用于恢复该状态。

# 总结

备忘录模式比较适用于功能比较复杂的，但需要维护或记录属性历史的类，或者需要保存属性只是众多属性中的一小部分时，Originator可以根据保存的Memento信息还原到前一状态。

## 优点

- 当一个对象的内部信息必须保存在发起人对象以外的地方，但是必须由发起人自己读取。使用备忘录模式可以把该对象的内部信息对其他对象屏蔽，恰当保持封装的边界。
- 客户端可以自行管理它们所需要的状态的版本。
- 当对象状态改变时，如果这个状态无效，可还原之前保存的状态。

## 缺点

- 对象状态需要完整存储到备忘录对象中或者多状态备份的话，需消耗较多资源。
- Creataker将一个备忘录保存起来的时候，可能不知道该状态占用的存储空间，无法提醒用户是否代价过大。