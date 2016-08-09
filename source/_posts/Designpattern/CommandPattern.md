title: 设计模式之命令模式
date: 2016-01-26 15:16:53
categories: [设计模式]
tags: [命令模式]
---
**将一个请求封装为一个对象，从而使你可用不同的请求对客户进行参数化；对请求排队或记录请求日志，以及支持可撤销的操作。**<!--more-->

这个概念初看还有点费解，其实主要是想讲“行为请求者”和“行为实践者”之间的关系一般都是“紧耦合”，命令模式做的是让它们之间松耦合，以便可进行“撤销、记录”等操作。

# 命令模式结构图

![命令模式结构图](http://7xpi7i.com1.z0.glb.clouddn.com/%E5%91%BD%E4%BB%A4%E6%A8%A1%E5%BC%8F%E7%BB%93%E6%9E%84%E5%9B%BE.jpg)

- Command：命令接口，声明执行操作的接口；
- ConcreteCommand：具体命令，就一个接收者对象绑定于一个动作，调用接收者相应的操作，实现Execute；
- Receiver：接收者，具体实施与执行一个请求相关的操作，任何类都可能作为一个接收者；
- Invoker：请求者，触发执行命令。

# 案例分析

电视的使用就是通过命令来进行的，开机、关机、切换频道等等，对应不同的命令。

1. Command接口，声明execute方法
```
public interface Command {
	void execute();
}
```

2. 创建一个TV类，有关机、开机、切换频道等方法
```
public class Tv {

	public void turnOn() {
		System.out.println("The televisino is on.");
	}

	public void turnOff() {
		System.out.println("The television is off.");
	}

	public void changeChannel(int channel) {
		System.out.println("Now TV channel is " + channel);
	}
}
```

3. 创建开机、关机、切换频道具体命令。
```
/**
 * 关机命令
 */
public class CommandOff implements Command {
	private Tv myTv;

	public CommandOff(Tv tv) {
		myTv = tv;
	}

	public void execute() {
		myTv.turnOff();
	}
}

/**
 * 频道切换命令
 */
public class CommandChange implements Command {
	private Tv myTv;

	private int channel;

	public CommandChange(Tv tv, int channel) {
		myTv = tv;
		this.channel = channel;
	}

	public void execute() {
		myTv.changeChannel(channel);
	}
}

/**
 * 开机命令
 */
public class CommandOn implements Command {
	private Tv myTv;

	public CommandOn(Tv tv) {
		myTv = tv;
	}

	public void execute() {
		myTv.turnOn();
	}
}
```

4. 创建一个遥控器Control，即Invoker，需要注意的是，真正发出命令的并不是Client，而是Invoker。
```
/**
 * 遥控器
 */
public class Control {
	private Command onCommand, offCommand, changeChannel;

	public Control(Command on, Command off, Command channel) {
		onCommand = on;
		offCommand = off;
		changeChannel = channel;
	}

	public void turnOn() {
		onCommand.execute();
	}

	public void turnOff() {
		offCommand.execute();
	}

	public void changeChannel() {
		changeChannel.execute();
	}
}
```

5. 客户端调用
```
public static void main(String[] args) {
	// 命令接收者
	Tv myTv = new Tv();
	// 开机命令
	CommandOn on = new CommandOn(myTv);
	// 关机命令
	CommandOff off = new CommandOff(myTv);
	// 频道切换命令
	CommandChange channel = new CommandChange(myTv, 2);
	// 命令控制对象
	Control control = new Control(on, off, channel);

	// 开机
	control.turnOn();
	// 切换频道
	control.changeChannel();
	// 关机
	control.turnOff();

}
```

6. 运行结果
```
The televisino is on.
Now TV channel is 2
The television is off.
```

# 总结

## 优点 

- 就是将行为请求者和行为实现者解耦。
- 容易设计一个命令队列。
- 在需要的情况下，可容易将命令记入日志。
- 容易实现撤销和重做。
- 增加具体命令类容易。

## 缺点

- 应该也是具体命令类过多时，会造成类的数量过多，增加系统复杂性。

## 适用场景

- 需要在不同的时间指定请求、将请求排队。
- 需要实现撤销功能时。
- 需要记录命令，以便系统崩溃时恢复。