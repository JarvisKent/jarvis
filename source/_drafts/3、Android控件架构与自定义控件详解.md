title: 3、Android控件架构与自定义控件详解
date: 2016-01-05 10:40:32
categories: [Android群英传]
tags: [android,控件，自定义,架构]
---

# Android控件架构

控件是每个AndroidApp必不可少的一部分。Android中的每个控件都会在界面中占得一块矩形的区域，控件大致被分为两类，即ViewGroup和View控件。<!--more-->

ViewGroup作为父控件可以包含多个View控件，并管理其包含的View控件。通过ViewGroup，整个界面上的控件形成一个树形结构（常被称为控件树），上层控件负责下层子控件的测量和绘制，并传递交互事件。

通常使用Activity中的findViewById()方法就是在控件树中以树的深度优先遍历查找对应元素。而每个控件树顶部，都有一个ViewParent对象作为整棵树的控制核心，所有交互事件都由它统一调度和分配，进而对整个视图进行整体控制。

![UI界面架构图](http://7xpi7i.com1.z0.glb.clouddn.com/%E7%95%8C%E9%9D%A2%E6%9E%B6%E6%9E%84.jpg)

如图所示，每个Activity都包含一个Window对象，通常由PhoneWindow来实现。PhoneWindow将一个DecorView设为整个应用窗口的根View。DecorView封装了一些窗口通用的方法。这边所有View的监听都是通过WindowManagerService来接收的，并通过Activity对象回调onClickListener。在这边屏幕分成了两部分，TitleView和ContentView，TitleView是应用的标题栏，ContentView则是放置我们具体要显示的布局内容，通过setContentView来设置，这也是为什么隐藏标题栏的requestWindowFeature()要放在setContentView()之前的原因了。

当程序在onCreate()中调用setContentView()后，ActivityManagerService会回调onResume()方法，此时系统才会把整个DecorView添加到PhoneWindow中，并显示出来，完成最终界面的绘制。

# View的测量和绘制

## View的测量

在Android中，绘制View前都必须对View进行测量，这个过程一般是在onMeasure()中进行的。Android提供了一个MeasureSpec类，我们可以借助它来测量View。

MeasureSpec是一个32位int值，高2位为测量的模式，低30位为测量的大小，使用位模式是为了提高并优化效率。

测量模式可以分为以下三种。
- EXACTLY

给出精确的数值，或者指定为match_parent（占据父View的大小）属性。

- AT_MOST

最大值，属性指定为wrap_content。随控件大小或内容变化而变化，只要不超过父控件允许的最大尺寸即可。

- UNSPECIFIED

不指定大小测量模式。通常在绘制自定义View时才会使用。

View类默认的onMeasure()方法只支持EXACTLY模式，自定义控件一般要重新此方法，不然就只能使用EXACTLY模式了。通过MeasureSpec这个类，我们可以获取测量模式和想要绘制的大小，进而控制View最后显示的大小。

接下来，我们通过一个简单的例子来展示如何进行View的测量。
- 重写onMeasure()方法。

查看super.onMeasure()方法，就会发现，系统其实是调用setMeasuredDimension(int measuredWidth,int measuredHeight)方法来设置宽高的。
所以，我们重写代码为
```
@Override
protected void onMeasure(int widthMeasureSpec,int heightMeasureSpec){
	setMeasuredDimension(measureWidth(widthMeasureSpec),measureHeight(heightMeasureSpec));
}
```
宽高通过调用我们自定义的方法，参数则是宽高的MeasureSpec对象，MeasureSpec对象包含测量的模式和测量值大小。
自定义方法的写法如下:
```
private int measureWidth(int measureSpec){
	int result = 0;
	//从MeasureSpec对象中获取具体测量模式和大小
	int specMode = MeasureSpec.getMode(measureSpec);
	int specSize = MeasureSpec.getSize(measureSpec);

	if(specMode == MeasureSpec.EXACTLY){
		result = specSize;
	}else{
		result = 200;
		if(specMode == MeasureSpec.AT_MOST){
			result.Math.min(result,specSize);
		}
		return result;
	}
}
```
measureHeight()的代码基本和上面的一致。这样的话：
- 当我们在布局中有给出宽高数值时，则按给出的数值显示；
- 如果给的是match_parent则占据整个父布局；
- 如果给的是wrap_content，则宽高设定都为200。
这样，就实现了自定义布局的宽高了。

## View的绘制

要绘制View就不得不提到Canvas对象了，想在界面上绘制图像，必须在Canvas上进行绘制。Canvas就像是一块画板，使用Paint就可以再上面作画。通常，需要继承View并重写onDraw()方法来完成绘图。

在onDraw()方法中有一个参数，它就是canvas对象。我们可以用它来绘图，如果想在其他地方绘图的话，我们需要创建一个Canvas对象。

在绘制过程中，我们并不直接在Canvas上绘图，而是在加载的bitmap上绘制，然后让View重绘，显示改变后的bitmap。

# ViewGroup的测量和绘制

## ViewGroup的测量

之前了解到ViewGroup会管理它包含的View，其中一项就是View的大小。当ViewGroup的大小为wrap_content时，ViewGroup就需要对子View进行遍历，来获取所有子View大小，以便确定自己的大小。在其他模式下，则根据指定值来设置自身大小。

ViewGroup遍历子View时，调用的是子View的Measure方法来测量的。当测量完后，要把View放到合适的位置，这个过程是View的Layout过程。在这个过程中，ViewGroup同样是遍历了子View的Layout方法，并指定具体显示位置，从而决定了布局位置。

自定义ViewGroup时，通常都需要重写onLayout()方法来控制其子View的显示位置的逻辑。如果需要支持wrap_content的话它还必须重写onMeasure()方法。

## ViewGroup的绘制

通常情况下不需要绘制，如果不指定它的背景颜色的话，onDraw()都不会调用。但是ViewGroup会使用dispatchDraw()来绘制子View，过程同样要遍历所有子View，并调用子View的绘制方法来完成绘制。

# 自定义View

在自定义View时，我们通常会去重写onDraw()方法来绘制View显示的内容。如果View要使用wrap_content属性，还要重写onMeasure()方法。另外，自定义View还可以设置新的属性配置。

在View中通常有以下一些比较重要的回调方法：
- onFinishInflate() : 从XML加载组件后回调
- onSizeChanged() : 组件大小改变时回调
- onMeasure() : 回调该方法来进行测量
- onLayout() : 回调该方法来确定显示位置
- onTouchEvent() : 监听到触摸事件时回调

创建自定义View并不需要重写所有方法，通常由三种方法来实现自定义的控件
- **对现有控件进行拓展**
- **通过组合来实现新的控件**
- **重写View来实现全新的控件**

## 对现有控件进行拓展

即在原生控件上改变UI或拓展功能。一般来说可以在onDraw()中进行拓展。
```
@Override
protected void onDraw(Canvas canvas){
	//在回调父方法之前，实现自己的逻辑，在TextView中来说是在文本内容生成前实现自己的逻辑
	super.onDraw(canvas);
	//在回调父方法之后，实现自己的逻辑，在TextView中来说是在文本内容生成后实现
}
```
案例可以参考我之前写的文章的前两个例子：[传送门](http://huangzihao.me/android/Android自定义控件之对原有控件的拓展/)

## 创建复合控件

复合控件主要是用于有重用功能的控件集合。这种控件需要继承一个合适的ViewGroup，再给它添加指定控件，从而组成新的复合控件。

### 组合控件

以一个有左按钮/右按钮/中间标题的TopBar为例。
1. 首先是设定相应的属性值：文字大小颜色等。
2. 接着是接口的定义，左右两个按钮都需要监听，则定义一个自定义的点击监听接口,来实现回调。
```
public interface topbarClickListner{
	void leftClick();
	void rightClick();
}
```
3. 暴露接口给调用者
```
private topbarClickListener mListener;
RightButton.setOnClickListener(new onClickListener(){
	@override
	pubic void onClick(View v){
		mListener.rightClick();
	}
});

LeftButton.setOnClickListener(new onClickListener(){
	@override
	pubic void onClick(View v){
		mListener.leftClick();
	}
});

public void setOnTopbarClickListener(topbarClickListener mListener){
	this.mListener = mListener;
}
```
4. 实现接口回调
```
topbar.setOnTopbarClickListener(
	new TopBar.topbarClickListener(){
		@Override
		public void rightClick(){
			
		}
		@Override
		public void leftClick(){

		}
	}
);
```
5. 还可以通过公共方法，动态修改UI模板中的UI，进一步提高可定制性。
```
public void setButtonVisable(int id,boolean flag){
	if(flag){
		if(id == 0)
		{
			leftButton.setVisibility(View.VISIBLE);
		}else
		{
			rightButton.setVisibility(View.VISIBLE);
		}
	}else{
		if(id == 0)
		{
			leftButton.setVisibility(View.GONE);
		}else
		{
			rightButton.setVisibility(View.GONE);
		}
	}
}
```
6. 引用UI模板,再xml中进行引用
```
<com.imooc.TopBar
	android:id="@+id/topBar"
	android:layout_width="match_parent"
	androdi:layout_height="40dp" />
```
7. 还可以写成一个布局文件，然后再其他布局文件中用include标签来引用
```
<include layout="@layout/topbar" />
```
## 重写View来实现全新的控件

当系统原生控件无法满足我们的需求时，我们可以完全创建一个新的自定义View来实现需要的功能。**创建一个自定义View的难点在于绘制控件和实现交互。**通常需要继承View类，并重写onDraw(),onMeasure()等来实现绘制逻辑，同时重写onTouchEvent()等实现交互逻辑。

案例参考：[传送门](http://huangzihao.me/android/Android自定义控件之实现全新的View/)

# 自定义ViewGroup

ViewGroup存在的目的就是为了**对其子View进行管理，为其子View添加显示，响应的规则**。
因此，，自定义的ViewGroup通常需要重写onMeasure()来对子View进行测量，重写onLayout()来确定子View的位置，重写onTouchEvent()方法增加响应事件。


# 事件拦截机制的分析