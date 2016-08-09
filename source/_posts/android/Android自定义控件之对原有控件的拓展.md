title: Android自定义控件之对原生控件的拓展
date: 2016-03-01 11:11:06
categories: [Android]
tags: [android,原生控件的拓展]
---

在Android开发中，原生控件时常会无法满足我们的需要。这时，我们就需要进行自定义控件了，而对原生控件的拓展即是解决方法之一。<!--more-->

# 对原生控件的拓展

对原生控件的拓展是指在Android提供的控件的基础上进行修改，实现符合需求的控件。做法是通过创建一个自定义的控件类，继承要进行拓展的控件的类。在继承来的方法中进行拓展。这样的好处在于，不仅控件的展示能符合要求，使用方法也和所继承的原生控件的使用方法一致。

# 案例

案例都是来源于最近正在看的书————《Android群英传》。

## 带矩形边框的文本

1. 创建一个自定义的CustomTextView继承TextView
```
public class CustomTextView extends TextView {
	private Paint paint1,paint2;
	public CustomTextView(Context context) {
		super(context);
		initView();
	}

	public CustomTextView(Context context, AttributeSet attrs) {

		super(context, attrs);
		initView();
	}

	public CustomTextView(Context context, AttributeSet attrs, int defStyleAttr) {
		super(context, attrs, defStyleAttr);
		initView();
	}
	private void initView(){
		paint1 = new Paint();
		paint1.setColor(Color.BLUE);
		paint1.setStyle(Paint.Style.FILL);

		paint2 = new Paint();
		paint2.setColor(Color.YELLOW);
		paint2.setStyle(Paint.Style.FILL);
	}
	@Override
	protected void onDraw(Canvas canvas) {
		//绘制外层矩形
		canvas.drawRect(0,0,getMeasuredWidth()+6,getMeasuredHeight()+6,paint1);
		//绘制内层矩形
		canvas.drawRect(3, 3, getMeasuredWidth() - 3, getMeasuredHeight() - 3, paint2);

		canvas.save();
		//绘制文字前平移3个像素
		canvas.translate(3, 0);
		//父类完成的方法，绘制文本
		super.onDraw(canvas);
		canvas.restore();
	}
}
```
2. 在布局中使用
```
<custom.CustomTextView
        android:id="@+id/customTextView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="自定义带矩形的边框" />
```

其实从上面的代码也可以看出，带边框就是一个小矩形叠在一个稍大点的矩形上形成的效果。

## 闪动的文字

渐变闪动，使用的是LinearGradient（又称线性渲染）来实现的。
```
public class CustomTextView2 extends TextView {

	private LinearGradient mLinearGradient;
	private Matrix mMatrix;
	private Paint paint;
	private int mViewWidth = 0;
	private int mTranslate = 0; 
	public CustomTextView2(Context context, AttributeSet attrs) {
		super(context, attrs);
	}
	@Override
	protected void onSizeChanged(int w, int h, int oldw, int oldh) {
		super.onSizeChanged(w, h, oldw, oldh);
		if(mViewWidth == 0){
			mViewWidth = getMeasuredWidth();
			  if (mViewWidth>0){
	                paint = getPaint();
	                mLinearGradient = new LinearGradient(
	                        0,
	                        0,
	                        mViewWidth,
	                        0,
	                        new int[]{Color.BLUE,0xffffffff,
	                                Color.BLUE},
	                        null,
	                        Shader.TileMode.CLAMP);
	                paint.setShader(mLinearGradient);
	                mMatrix = new Matrix();
	            }
	        }
	}
	
	@Override
	protected void onDraw(Canvas canvas) {
		super.onDraw(canvas);
		  if (mMatrix != null){
	            mTranslate += mViewWidth / 5 ;
	            if (mTranslate > 2 * mViewWidth){
	                mTranslate = - mViewWidth;
	            }
	            mMatrix.setTranslate(mTranslate,0);
	            mLinearGradient.setLocalMatrix(mMatrix);
	            postInvalidateDelayed(100);
	        }
	}
}
```
使用方法参照上一个案例，对原生控件进行拓展的学习就到这里结束了，至于如何灵活运用呢？有待以后实战开发中来挖掘。