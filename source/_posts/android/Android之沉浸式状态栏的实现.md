title: Android之沉浸式状态栏的实现
date: 2016-03-12 21:07:30
categories: [Android]
tags: [沉浸式状态栏]
---

沉浸式状态栏确切的说应该叫做透明状态栏。一般情况下，状态栏的底色都为黑色，而沉浸式状态栏则是把状态栏设置为透明或者半透明。<!--more-->

# 为什么要使用沉浸式状态栏

沉浸式状态栏是从android Kitkat（Android 4.4）开始出现的,它可以被设置成与APP顶部相同的颜色，这就使得切换APP时，整个界面就好似切换到了与APP相同的风格样式一样。在内容展示上会显得更加美观。

# 实现方式

需要注意的是，**因为沉浸式状态栏是在Android4.4的时候出现的，所以只有4.4及以后的版本才能使用。**实现沉浸式状态栏的方式有几种，最简单的方式就是使用系统提供的方式进行实现。

## 系统提供的方法

1. 在需要实现沉浸式状态栏的Activity的布局中添加以下参数

```
android:fitsSystemWindows="true"
android:clipToPadding="true"
```
2. 代码中对当前系统进行判断，符合要求（即系统是4.4或以上的版本）则设置成透明状态栏
```
private void initState() {
  if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
 		//透明状态栏
    	getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
        //透明导航栏
        getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_NAVIGATION);
  }
}
```
3. 在Activity的setContentView()方法后面调用第二步骤的方法即可。

我在使用小米手机测试这个方法的时候出现了两种情况

- 当我没有在布局中设置clipToPadding为true的时候，会对应用的顶部Toolbar进行拉伸；
- 当我在布局中两个参数都进行设置后，顶部状态栏变成了白色。

因此，我使用了第二种方法：添加隐藏布局，动态调整布局高度适应状态栏

## 动态适应状态栏高度设置布局

因为第一部会出现的拉伸问题，所以我在Toolbar上方添加了一个隐藏LinearLayout，动态设置与状态栏等高，抵消Toolbar拉伸。

1. 在xml布局的顶部添加一个隐藏布局
```
<LinearLayout
        android:id="@+id/ll"
        android:layout_width="fill_parent"
        android:layout_height="1dp"
        android:orientation="vertical"
        android:background="#e7abff"<!--颜色被我设置成了与Toolbar一样-->
        android:visibility="gone">
    </LinearLayout>
```
2. 代码中判断系统版本，并通过反射设置隐藏布局高度
```
/**
* 动态的设置状态栏  实现沉浸式状态栏
*/
private void initState() {

    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
        //透明状态栏
        getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
        //透明导航栏
        getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_NAVIGATION);
        
        LinearLayout linear_bar = (LinearLayout) findViewById(R.id.ll_bar);
        linear_bar.setVisibility(View.VISIBLE);
        //获取到状态栏的高度
        int statusHeight = getStatusBarHeight();
        //动态的设置隐藏布局的高度
        LinearLayout.LayoutParams params = (LinearLayout.LayoutParams) linear_bar.getLayoutParams();
        params.height = statusHeight;
        linear_bar.setLayoutParams(params);
    }
}
/**
* 通过反射的方式获取状态栏高度
* @return
*/
private int getStatusBarHeight() {
    try {
        Class<?> c = Class.forName("com.android.internal.R$dimen");
        Object obj = c.newInstance();
        Field field = c.getField("status_bar_height");
        int x = Integer.parseInt(field.get(obj).toString());
        return getResources().getDimensionPixelSize(x);
    } catch (Exception e) {
        e.printStackTrace();
    }
     return 0;
}
```

3. 上述步骤完成后同样也是在Activity中的setContentView()后进行调用即可。

## 使用第三方库进行实现

在github上也有开源的库提供使用，如：[SystemBarTint](https://github.com/jgilfelt/SystemBarTint)

不得不说开源的力量是如此的强大。使用方法在连接里有介绍，这里就不复述了。