title: 设计模式之状态模式
date: 2016-01-22 09:32:27
categories: [设计模式]
tags: [状态模式]
---
**当一个对象的内在状态改变时允许改变其行为，这个对象看起来像是改变了其类。**<!--more-->

# 状态模式结构介绍

![状态模式结构图](http://7xpi7i.com1.z0.glb.clouddn.com/%E7%8A%B6%E6%80%81%E6%A8%A1%E5%BC%8F%E7%BB%93%E6%9E%84%E5%9B%BE.jpg)

- Context：上下文，定义客户端所感兴趣的接口，并保留一个具体状态类的实例。这个具体状态类的实例给出此环境对象现有状态。

- State：定义一个接口，用以封装Context对象的一个特定状态所对应的行为。

- ConcreteState：具体状态类，实现了Context的一个状态所对应的行为。

# 案例分析

[案例来源参考](http://www.cnblogs.com/java-my-life/archive/2012/06/08/2538146.html)

在一个投票系统中，为了防止恶意刷票，系统做了如下处理，同一个用户对同一个选项投票的时候，超过5票定性为恶意投票，取消之前投票，超过8次，拉入黑名单，取消一切投票资格。

1. 创建State接口：VoteState
```
public interface VoteState {
	 /**
     * 处理状态对应的行为
     * @param user    投票人
     * @param voteItem    投票项
     * @param voteManager 投票上下文，用来在实现状态对应的功能处理的时候，
     *                         可以回调上下文的数据
     */
    public void vote(String user,String voteItem,VoteManager voteManager);
}
```

2. 创建Context类，VoteManager:
```
public class VoteManager {
	/**
	 * 持有状态处理对象
	 */
	private VoteState state = null;
	/**
	 * 记录用户投票的结果，<用户名，投票选项>
	 */
	private Map<String,String> mapVote = new HashMap<String,String>();
	/**
	 * 记录投票次数<用户名，投票数>
	 */
	private Map<String,Integer> mapVoteCount = new HashMap<String,Integer>();

	/**
	 * 获取投票结果
	 */
	public Map<String,String> getMapVote(){
		return mapVote;
	}
	/**
	 * 投票
	 * @param user  投票人
	 * @param item  选项
	 */
	public void vote(String user,String item){
		//1、记录用户增加投票次数
		Integer oldVoteCount = mapVoteCount.get(user);
		if(oldVoteCount == null)
			oldVoteCount = 0;
		oldVoteCount += 1;
		mapVoteCount.put(user, oldVoteCount);

		//2、判断该用户的投票类型，就相当于判断对应的状态
		//正常投票、重复投票、恶意投票还是黑名单
		if(oldVoteCount == 1){
			state = new NormalVoteState();
		}
		else if(oldVoteCount >1 && oldVoteCount < 5){
			state = new RepeatVoteState();
		}
		else if(oldVoteCount >=5 && oldVoteCount <8){
			state = new SpiteVoteState();
		}else if(oldVoteCount > 8){
			state = new BlackVoteState();
		}
		state.vote(user, item, this);
	}
}
```
3. 创建ConcreteState，具体状态类有正常投票、重复投票、恶意投票、黑名单四种:
```
public class NormalVoteState implements VoteState {

	@Override
	public void vote(String user, String voteItem, VoteManager voteManager) {
		 //正常投票，记录到投票记录中
        voteManager.getMapVote().put(user, voteItem);
        System.out.println("恭喜投票成功");
	}

}

public class RepeatVoteState implements VoteState {

	@Override
	public void vote(String user, String voteItem, VoteManager voteManager) {
		//重复投票不做处理
		System.out.println("请不要重复投票");
	}

}

public class SpiteVoteState implements VoteState {

	@Override
	public void vote(String user, String voteItem, VoteManager voteManager) {
		String str = voteManager.getMapVote().get(user);
		if(str != null)
			voteManager.getMapVote().remove(user);
		System.out.println("恶意投票，取消资格");
	}

}

public class BlackVoteState implements VoteState {

	@Override
	public void vote(String user, String voteItem, VoteManager voteManager) {
		System.out.println("拉入黑名单");
	}

}
```

4. 客户端调用代码:
```
public static void main(String[] args) {
		VoteManager vm = new VoteManager();
		//张三要投自己9票
		for (int i = 0; i < 9; i++) {
			vm.vote("张三", "张三");
		}
	}
```

5. 代码运行结果：
```
恭喜投票成功
请不要重复投票
请不要重复投票
请不要重复投票
恶意投票，取消资格
恶意投票，取消资格
恶意投票，取消资格
恶意投票，取消资格
拉入黑名单
```

# 总结

在很多情况下，**一个对象的行为取决于它的一个或多个变化的属性，这些属性我们称之为状态**，这个对象称之为状态对象。对于状态对象来说，它的行为依赖于它的状态，当它在与外部事件产生互动的时候，其内部状态就会发生改变，从而使得他的行为也随之发生改变。

而状态模式主要解决的是**当控制一个对象状态改变的条件表达式过于复杂时的情况**。把状态的判断逻辑转移到表示不同状态的一系列类当中，可以把复杂的判断逻辑简化。好处在于将与特定状态相关的行为局部化，并且将不同状态的行为分割开来，说白了就是为了消耗庞大的条件分支语句。

所以，当一个对象的行为取决于它的状态，并且它必须在运行时刻根据状态改变它的行为时，就可以考虑用状态模式了。

## 优点

- 封装了转换规则。
- 枚举可能的状态，在枚举状态之前需要确定状态种类。
- 将所有与某个状态有关的行为放到一个类中，并且可以方便地增加新的状态，只需要改变对象状态即可改变对象的行为。 
- 允许状态转换逻辑与状态对象合成一体，而不是某一个巨大的条件语句块。 
- 可以让多个环境对象共享一个状态对象，从而减少系统中对象的个数。

## 缺点

- 状态模式的使用必然会增加系统类和对象的个数。 
- 状态模式的结构与实现都较为复杂，如果使用不当将导致程序结构和代码的混乱。 
- 状态模式对“开闭原则”的支持并不太好，对于可以切换状态的状态模式，增加新的状态类需要修改那些负责状态转换的源代码，否则无法切换到新增状态；而且修改某个状态类的行为也需修改对应类的源代码。

## 使用场景

- 对象的行为依赖于它的状态（属性）并且可以根据它的状态改变而改变它的相关行为。 
- 代码中包含大量与对象状态有关的条件语句。