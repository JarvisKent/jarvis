title: (1)、创建和销毁对象
date: 2016-02-4 09:29:49
categories: [Effective Java笔记]
tags: [effect java,对象]
---
何时以及如何创建对象，何时以及如何避免创建对象，如何确保它们能够适时地销毁，以及如何管理对象销毁之前必须进行的各种清理动作。<!--more-->

# 考虑用静态工厂方法代替构造器

```
public static Boolean valueOf(boolean b){
	return b ?Boolean.TRUE : Boolean.FALSE;
}
```
并非是设计模式中的工厂方法。用这种静态工厂方法有几个优势。

- 它们有名称，易于阅读。
- 不必在每次调用它们的时候都要创建一个新的对象。有助于类总能严格控制在某个时刻哪些实例应该存在。
- 它们可以返回原返回类型的任何子类型对象。
- 在创建参数化类型实例的时候，它们使代码变得更加简洁。

主要缺点在于：

- 类如果不含公有的或者受保护的构造器，就不能被子类化。
- 它们与其他的静态方法实际上没有任何区别。对想查明如何实例化一个类造成困难。

下面是一些静态工厂方法的惯用名称：
1. valueOf:该方法返回的实例与它的参数具有相同值。实际上是类型转换方法。
2. of：valueOf的一种更为简洁的替代。
3. getInstance：返回的实例是通过方法的参数描述的，但不能够说与参数具有相同的值。对于Singleton来说，该方法没有参数，返回唯一的实例。
4. newInstance：像getInstance一样，但newInstance能够确保返回的每个实例都与所有其他实例不同。
5. getType：像getInstance一样，但是在工厂方法处于不用的类中的时候使用。Type表示工厂方法返回的对象类型。
6. newType：像newInstance一样，但是在工厂方法处于不用的类中的时候使用。Type表示工厂方法返回的对象类型。

# 遇到多个构造器参数时考虑用Builder

静态工厂和构造器有个共同局限性：不能扩展大量的可选参数。

当参数过多时，**重叠构造器**模式可行，但是客户端代码会很难编写，且难以阅读。而**创建JavaBean**虽然会比较容易且可读性也会增强，但是自身却有个严重的缺点。在构造过程中JavaBean可能处于不一致状态。却JavaBeans模式组织了把类做成不可变的可能。需要额外的工作来确保它的线程安全。

有一种可替代方法，既能保证像重叠构造器模式那样的安全性，也能像JavaBeans模式的良好可读性。就是Builder模式。不直接生成对象，而是让客户利用必要的参数调用构造器，得到一个builder对象。
```
public class NutritionFacts{
	private final int servingSize;
	private final int servings;
	private final int calories;
	private final int fat;
	private final int sodium;
	private final int carbohydrate;

	public static class Builder{
		private final int servingSize;
		private final int serving;

		private int calories     = 0;
		private int fat          = 0;
		private int carbohydrate = 0;
		private int sodium       = 0;

		public Builder(int servingSize, int servings){
			this.servingSize = servingSize;
			this.servings    = servings;
		}

		public Builder calories(int val){
			calories = val;
			return this;
		}

		public Builder fat(int val){
			fat = val;
			return this;
		}

		public Builder carbohydrate(int val){
			carbohydrate = val;
			return this;
		}

		public Builder sodium(int val){
			sodium = val;
			return this;
		}

		public NutritionFacts build(){
			return new NutritionFacts(this);
		}
	}

	private NutritionFacts(Builder builder){
		servingSize = builder.servingSize;
		servings    = builder.servings;
		calories    = builder.calories;
		fat         = builder.fat;
		sodium      = builder.sodium;
		carbohydrate= builder.carbohydrate;
	}
}
//客户端使用的话
NutritionFacts cocaCola = new NutritionFacts.Builder(240,8).
calories(100).sodiumm(35).carbohydrate(27).build();
```

builder的优势在于可拥有多个可变参数。灵活，可利用耽搁builder构建多个多谢。我们可以使用一个泛型来满足所有的builder，无论构建哪种类型的对象:
```
public interface Builder<T>{
	public T build();
}
```
key生命NutritionFacts.Builder类来实现Builder<NutritionFacts>。

Builder模式的不足在于为了创建对象，需要先创建它的Builder。在十分注重性能的情况下，可能成问题。且它比重叠构造器更冗长，因此只在很多参数时才使用。

**当类的构造器或者静态工厂中具有多个参数，可以考虑Builder模式，特别是大多数参数都是可选的时候。因为它更易阅读和编写，也比JavaBeans安全。**

# 用私有构造器或者枚举类型强化Singleton属性

Singleton通常由懒汉(第一次使用)和饿汉(类生成实例时)两种写法。在Java1.5后还有单元素枚举类型的写法
```
public enum Elvis{
	INSTANCE;
	public void leaveTheBuilding(){...}
}
```
这种单元素的枚举类型已经成为实现Singleton的最佳方法。绝对防止多次实例化，即使是面多复杂的序列化或者反射攻击时。

# 通过私有构造器强化不可实例化的能力

- 当一个类缺少显式构造器时，编译器会自动提供一个公有、无参的缺省构造器。
- 企图通过将类做成抽象类来强制该类不可被实例化，是行不通的。
给类添加一个私有（private）的构造器，保证该类在任何情况下都不会被实例化。最好在代码中再增加一条注释如:
```
public class UtilityClass{
	//Suppress default constructor for noninstantiability
	private UtilityClass(){
	throw new AssertionErrot();
	}
	... //Remainder omitted
}
```
这有个副作用，它使得类不能被子类化。所有构造器都必须显式或隐式地调用超类构造器，这种情况下，子类就没有可访问的超类可调用了。

# 避免创建不必要的对象

## 最极端的例子

```
//error
String str = new String("abc");//Don't do this!
//true
String str = "abc";
```
传递给String构造器的参数("abc")本身就是一个String实例，功能等同于构造器创建的所有对象。如果这种用法是在一个循环中，或者是在一个被频繁调用的方法中，就会创建很多不必要的实例了。

## 另一个例子

下面的例子是检验一个人是否是1946到1964出生的。
```
public class Person{
	private final Date birthDate;

	public boolean isBabyBoomer(){
		Calendar gmtCal = 
			Calendar.getInstance(TimeZone.getTimeZone("GMT"));
			gmtCal.set(1946,Calendar.JANUARY,1,0,0,0);
			Date boomStart = gmtCal.getTime();
			gmtCal.set(1965,Calendar.JANUARY,1,0,0,0);
			Date boomEnd = gmtCal.getTime();
			return birthDate.compareTo(boomStart) >= 0 &&
				birthDate.compareTo(boomEnd) < 0;
				}
}

```
isBabyBoomer每次被调用都会新建一个Calendar，一个TimeZone和两个Date实例。我们可以用initializer来避免这种低效率。
```
class person{
	private final Date birthDate;

	private static final Date BOOM_START;
	private static final Date BOOM_END;

	static{
		Calendar gmtCal = 
			Calendar.getInstance(TimeZone.getTimeZone("GMT"));
			gmtCal.set(1946,Calendar.JANUARY,1,0,0,0);
			BOOM_START = gmtCal.getTime();
			gmtCal.set(1965,Calendar.JANUARY,1,0,0,0);
			BOOM_END = gmtCal.getTime();
	}

	public boolean isBabyBoomer(){
		return birthDate.compareTo(BOOM_START) >= 0 &&
			birthDate.compareTo(BOOM_END) < 0;
	}
}
```
不仅提高性能，也使得代码含义更清晰。**但是如果isBabyBoomer方法都不被调用的话，那就没有初始化BOOM_START和BOOM_END的必要了。可以改成通过lazily initializing避免不必要的初始化工作。**
如果同时提供静态工厂方法和构造器不可变类，通常使用静态工厂方法，避免创建不必要的对象。

Java有一种创建多余对象的新方法，称作autoboxing。允许将基本类型和autoboxing混用。
```
public static void main(String[] args){
	Long sum = 0L;
	for(long i = 0; i < Integer.MAX_VALUE; i++){
		sum += i;
	}
	System.out.println(sum);
}
```
虽然答案会是对的。但是因为被声明成了Long而非long。意味着构建了大约2的31次方个多余的Long实例。**我们应该优先使用基本类型而不是autoboxing基本类型，要当心无意识的autoboxing。**

# 消除过期的对象引用

## 内存泄漏常见来源：引用
```
public class Stack{
	private Object[] elements;
	private int size = 0;
	private static final int DEFAULT_INITIAL_CAPACITY = 16;
	public Stack(){
		elements = new Object[DEFAULT_INITIAL_CAPACITY];
	}
	public void push(Object e){
		ensureCapacity();
		elements[size++] = e;
	}
	public Object pop(){
		if(size == 0)
			throw new EmptyStackException();
		return elements[--size];
	}

	private void ensureCapacity(){
		if(elements.length == size)
			elements = Arrays.copyOf(elements,2 * size +1);
	}
}
```

这个并没有明显错误，但是在极端情况下，可能会导致Disk Paging，甚至OOM(Out Of Memory)的情况。

一个栈先增长再收缩，弹出来的对象不会被当作垃圾回收，且使用栈的程序也不会再引用这些对象。修复也很简单，即对象过期清空即可。
```
public Object pop(){
	if(size == 0)
		throw new EmptyStackException();
	Object result = elements[--size];
	elements[size] = null; //清空对象引用。
	return result;
}
```
清空引用有另一个好处：被错误的解除引用，程序会立刻抛出空指针异常，不会默默地继续运行下去。

清空对象引用最好的方法是让包含该引用的变量结束其生命周期。**只要类是自己管理内存，就应该警惕内存泄漏问题。**

## 常见内存泄漏的来源：缓存

对象引用放到缓存中，很容易被遗忘掉。有几种可能的解决方案
1. 用WeakHashMap代表缓存，缓存的项过期会自动删除。
2. 由一个后台线程来时不时清理缓存，或者在添加新条目到缓存时清理下。

## 常见内存泄漏来源： 监听器和其他回调

确保回调能被垃圾回收的最佳方式是只保存weak reference（弱引用），比如，只将它们保持为WeakHashMap中的key。

# 避免使用终结方法

finalizer通常是不可预测，应该尽量避免使用。为类提供终结方法，可能会随意的延迟它的实例的回收过程。**不应该依赖终结方法来更新重要的持久状态**。例如，依赖终结方法来释放共享资源上（如数据库）的永久锁，很容易让整个分布式系统垮掉。

System.gc和System.runFinalization虽然可以增加终结方法被执行的机会，但不保证一定会被执行。而可以保证被执行的方法是System.runFinalizersOnExit和Runtime.runFinalizersOnExit已经废弃。

使用finalizer有一个严重的性能损失。会使创建和销毁对象时间增长。

如果需要终止对象中封装的资源，只需要提供一个显式的终止方法，且需要注意一个细节，该实例必须记录下自己是否被终止了。使用这种方法的例子有InputSteam、OutputSteam等的close方法，还有Timer的cancel方法。**一般与try-finally结构结合使用，以确保及时终止。**

## 终结方法的用途

1. 当对象所有者忘记调用前面段落中的显式终止方法时，终结方法可以充当“safety net”。**当终结方法发现资源未被终止，应该在日志中记录一条警告**。

2. 与对象的native peer（本地对等体）有关。本地对等体是一个native object，普通对象通过本地方法委托给本地对象。因为本地对等体不是普通对象，所有gc不知道它的存在。在本地对等体不拥有关键资源的前提下，终结方法是执行这项任务的最合适工具。如果本地对等体拥有必须及时终止的资源，则该类需要具有显式终止方法。

很重要的一点**终结方法链（finalizer chaining）**不会被自动执行。如果类有终结方法，且子类覆盖了终结方法，子类的终结方法就必须手动调用超类的终结方法。如**在一个try中终结子类，在相应的finally中调用超类的终结方法。**这样可以保证：**即使子类终结过程有异常，超类的终结方法也会被执行。**

如果忘了手工调用超类终结方法，也有防范的措施，代价是为每个将被终结的对象创建一个附加对象。把终结方法放在一个匿名类中，该匿名类的用途是终结它的外围实例，该匿名类的单个实例被称为“方法终结者（finalizer guardian）”。外围类的每个实例都会创建这样一个守卫者。
```
//Finalizer Guardian idiom
public class Foo{
	private final Object finalizerGuardian = new Object(){
		@Override
		protected void finalize() throws Throwable{
		//Finalizer outer Foo object
		}
	}
}
```

- **除非是作为safety net或者为了终止非关键本地资源，否则不使用终结方法。**
- **既然使用了，要记得调用super.finalize。作为safety net要记得记录终结方法的非法用法。**
- **需要把终结方法和公有的非final类关联，考虑使用finalizer guardian，确保子类未调用super.finalize时，该终结方法也会被执行。**