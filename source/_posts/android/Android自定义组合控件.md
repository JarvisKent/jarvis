title: Android自定义控件之组合控件
date: 2016-02-22 10:07:39
categories: [Android]
tags: [自定义View]
---
据说，学习一样新的东西，要带着三个问题：
1. 这东西是什么；
2. 这东西有什么用；
3. 这东西怎么用。<!--more-->
这次的笔记就用这种方式记录试试。

# 组合控件

组合控件其实就是使用Android原生控件组成一个功能完整的符合需求的控件并将其封装成面向对象的类来使用。

# 组合控件的作用

为什么会有组合控件呢？因为很多时候，SDK自带的控件和都不能满足我们的需求。这个时候，我们就需要自己绘制一个复合需求的控件来使用了。

比如，每个页面都需要一个标题栏，标题栏要涵盖app名称，返回按钮、退出按钮、侧边栏按钮等等。或者要实现一个带删除按钮的EditText等等。

# 组合控件的简单实现

一个简单的需求，应用中的每个页面都要有标题栏，标题栏要显示应用的名称，名称后面是实时时间展示。
我们用组合控件来实现。需要几个步骤：
- 创建需要的布局界面
- 创建封装类来实现该布局功能
- 在需要的地方调用

## 布局的创建

先创建一个符合要求的页面上要显示的样式来。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="56dp"
    android:background="@android:color/darker_gray"
    android:gravity="center_vertical|center_horizontal"
    android:orientation="horizontal"
    android:paddingLeft="8dp" >

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/app_name"
        android:textSize="18sp" />

    <TextView
        android:id="@+id/current_time"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingLeft="10dp"
        android:text="@string/app_time" />

</LinearLayout>
```
其中的app_name和app_time是String中的
```
<resources>
    <string name="app_name">组合控件</string>
    <string name="app_time">2016-03-16  16:45:10</string>
</resources>
```
## 创建封装类

创建一个类来实现标题栏需要实现的功能，这边主要实现的是时间的实时更新。因为布局我们用的是LinearLayout。所以，创建的类也需要继承LinearLayout。
```
public class TitleView extends LinearLayout {

	private TextView currentTime;

	public TitleView(Context context, AttributeSet attrs) {
		super(context, attrs);
		LayoutInflater.from(context).inflate(R.layout.title, this);
		currentTime = (TextView) findViewById(R.id.current_time);
		new TimeThread().start(); //启动新的线程
	}
	class TimeThread extends Thread{
		@Override
		public void run() {
			do {
				try {
					Thread.sleep(1000);
					Message msg = new Message();
					msg.what = 1;  //消息(一个整型值)
					mHandler.sendMessage(msg);// 每隔1秒发送一个msg给mHandler
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			} while (true);			
		}
	}

	public void setCurrentTime(CharSequence sysTimeStr){
		currentTime.setText(sysTimeStr);
	}

	//在主线程里面处理消息并更新UI界面
	private Handler mHandler = new Handler(){
		@Override
		public void handleMessage(Message msg) {

			super.handleMessage(msg);
			switch (msg.what) {
			case 1:
				long sysTime = System.currentTimeMillis();
				CharSequence sysTimeStr = DateFormat.format("yyyy-MM-dd hh:mm:ss", sysTime);
				setCurrentTime(sysTimeStr); //更新时间
				break;
			default:
				break;
			}
		}
	};
}
```
到这里一个符合要求的组合控件就创建好了。

## 调用的控件

接下来，就是在需要的地方进行调用了，只要在每个有展示标题栏的页面的布局中调用如下代码就可以了。
```
<com.gotop.customview.TitleView
    android:layout_width="match_parent"
    android:layout_height="match_parent" >
</com.gotop.customview.TitleView>
```