title: Android自定义控件之实现全新的View
date: 2016-03-02 21:52:42
categories: [Android]
tags: [android,实现全新的View]
---

当原生控件不符合需求，并且进行拓展或组合也无济于事的时候，就需要自己来绘制一个控件了。<!--more-->

# 全新的View

通过源码，我们可以看到所有的控件都是通过继承View类来实现的。所以，我们要创建全新的控件，也是要通过继承View来实现。

# 案例

案例同样来自《Android群英传》。

## 绘制圆弧文本

唯恐文字描述有误，先放上效果图看看效果
![圆弧效果图](http://7xpi7i.com1.z0.glb.clouddn.com/circleprogressview.png)

```
public class CircleProgressView extends View {
	private int mMeasureHeigth;
	private int mMeasureWidth;

	private Paint mCirclePaint;
	private float mCircleXY;
	private float mRadius;

	private Paint mArcPaint;
	private RectF mArcRectF;
	private float mSweepAngle;
	private float mSweepValue = 66;

	private Paint mTextPaint;
	private String mShowText;
	private float mShowTextSize;
	public CircleProgressView(Context context, AttributeSet attrs,
			int defStyleAttr) {
		super(context, attrs, defStyleAttr);
	}

	public CircleProgressView(Context context, AttributeSet attrs) {
		super(context, attrs);
	}

	public CircleProgressView(Context context) {
		super(context);
	}

	@Override
	protected void onMeasure(int widthMeasureSpec,
			int heightMeasureSpec) {
		mMeasureWidth = MeasureSpec.getSize(widthMeasureSpec);
		mMeasureHeigth = MeasureSpec.getSize(heightMeasureSpec);
		setMeasuredDimension(mMeasureWidth, mMeasureHeigth);
		initView();
	}

	@Override
	protected void onDraw(Canvas canvas) {
		super.onDraw(canvas);
		// 绘制圆
		canvas.drawCircle(mCircleXY, mCircleXY, mRadius, mCirclePaint);
		// 绘制弧线
		canvas.drawArc(mArcRectF, 270, mSweepAngle, false, mArcPaint);
		// 绘制文字
		canvas.drawText(mShowText, 0, mShowText.length(),
				mCircleXY, mCircleXY + (mShowTextSize / 4), mTextPaint);
	}

	private void initView() {
		float length = 0;
		if (mMeasureHeigth >= mMeasureWidth) {
			length = mMeasureWidth;
		} else {
			length = mMeasureHeigth;
		}

		mCircleXY = length / 2;
		mRadius = (float) (length * 0.5 / 2);
		mCirclePaint = new Paint();
		mCirclePaint.setAntiAlias(true);
		mCirclePaint.setColor(getResources().getColor(
				android.R.color.holo_blue_bright));

		mArcRectF = new RectF(
				(float) (length * 0.1),
				(float) (length * 0.1),
				(float) (length * 0.9),
				(float) (length * 0.9));
		mSweepAngle = (mSweepValue / 100f) * 360f;
		mArcPaint = new Paint();
		mArcPaint.setAntiAlias(true);
		mArcPaint.setColor(getResources().getColor(
				android.R.color.holo_blue_bright));
		mArcPaint.setStrokeWidth((float) (length * 0.1));
		mArcPaint.setStyle(Style.STROKE);

		mShowText = setShowText();
		mShowTextSize = setShowTextSize();
		mTextPaint = new Paint();
		mTextPaint.setTextSize(mShowTextSize);
		mTextPaint.setTextAlign(Paint.Align.CENTER);
		
	}

	private float setShowTextSize() {
		this.invalidate();
		return 50;
	}

	private String setShowText() {
		this.invalidate();
		return "ANDROID SKILL";
	}

	public void forceInvalidate() {
		this.invalidate();
	}

	public void setSweepValue(float sweepValue) {
		if (sweepValue != 0) {
			mSweepValue = sweepValue;
		} else {
			mSweepValue = 25;
		}
		this.invalidate();
	}
}
```

## 动态音频条形图

![音频条形图效果图](http://7xpi7i.com1.z0.glb.clouddn.com/%E8%87%AA%E5%AE%9A%E4%B9%89%E5%85%A8%E6%96%B0View.png)

```
public class CustomView extends View {

	private int mRectCount;
	private Paint mPaint;
	private int mRectHeight;
	private int offset = 5;
	private int mWidth;
	private double mRandom;
	private int mRectWidth;
	public CustomView(Context context) {
		super(context);
		initView();
	}
	public CustomView(Context context, AttributeSet attrs) {
		super(context, attrs);
		initView();
	}
	public CustomView(Context context, AttributeSet attrs,
			int defStyleAttr) {
		super(context, attrs,defStyleAttr);
		initView();
	}
	private void initView() {
		mPaint = new Paint();
		mPaint.setColor(Color.BLUE);
		mPaint.setStyle(Paint.Style.FILL);
		mRectCount = 12;		
	}
	@Override
	protected void onSizeChanged(int w, int h, int oldw, int oldh) {
		super.onSizeChanged(w, h, oldw, oldh);
		mWidth = getWidth();
		mRectHeight = getHeight();
		mRectWidth = (int)(mWidth * 0.6 / mRectCount);
		LinearGradient mLinearGradient = new LinearGradient(
				0,
				0,
				mRectWidth,
				mRectHeight,
				Color.RED,
				Color.BLUE,
				Shader.TileMode.CLAMP
				);	
		mPaint.setShader(mLinearGradient);
	}
	@Override
	protected void onDraw(Canvas canvas) {
		super.onDraw(canvas);
		for (int i = 0; i < mRectCount; i++) {
			mRandom = Math.random();
			//每个条形的高
			float currentHeight = (float) (mRectHeight * mRandom);
			canvas.drawRect(
					(float) (mWidth * 0.4 / 2 + mRectWidth * i + offset),
					currentHeight,
					(float) (mWidth * 0.4 / 2 + mRectWidth * (i + 1)),
					mRectHeight,
					mPaint);
		}
		//延迟重绘
		postInvalidateDelayed(300);

	}
}
```
自己创建的控件用法与原生控件大同小异，可进行参照。