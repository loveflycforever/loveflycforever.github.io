---
layout: post
title:  "实现逐帧动画和补间动画两种动画效果"
date:   2015-10-25
tags: [Android]
comments: true
---
1.逐帧动画（Frame Animation）

通常在Android项目的res/drawable/目录下面定义逐帧动画的XML模板文件。编码的时候，需要在动画模板文件的<animation-list>标签中依次放入需要播放的图片，并设置好播放的间隔时间。

``` xml
<animation-list
	xmlns:android="http://schemas.android.com/apk/res/android"
	android:oneshot="false">
	<item android:drawable="@drawable/a001" android:duration="100"/>
	<item android:drawable="@drawable/a002" android:duration="100"/>
	<item android:drawable="@drawable/a003" android:duration="100"/>
	<!-- （省略） -->
</animation-list>
```

注意：逐帧动画并不能独立使用，动画效果的显示还是要借助于ImageView图像控件。

举例：(ImageView元素的id为m_image_view，上面的动画模板文件名为m_frame_animation)

``` java
public class MainActivity extends Activity {

	AnimationDrawable mAnimationDrawable;	

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		ImageView mImageView = (ImageView) findViewById(R.id.m_image_view);
		mImageView.setBackgroundResource(R.drawable.m_frame_animation);
		mAnimationDrawable = mImageView.getBackground();
		mAnimationDrawable.start();
	}

	@Override
	public void onPause() {
		super.onPause();
		mAnimationDrawable.stop();
	}

}
```

2.补间动画（Tween Animation）

逐帧动画通过连续播放图片来模拟动画的效果，而补间动画则是通过在两个关键帧之间补充简便的动画效果来实现的。

目前Android应用框架支持的补间动画效果有以下5种。具体实现在android.view.animation类库中。

- AlphaAnimation：透明度（alpha）渐变效果，对应<alpha/>标签。

- TranslateAnimation：位移渐变，需要指定移动点的开始和结束坐标，对应<translate/>标签。

- ScaleAnimation：缩放渐变，可以指定缩放的参考点，对应<scale/>标签。

- RotateAnimation：旋转渐变，可以指定旋转的参考点，对应<rotate/>标签。

- AnimationSet：组合渐变，支持组合多种渐变效果，对应<set/>标签。

补间动画的效果同样可以使用XML语言来定义，这些动画模板文件通常会被放在Android项目的res/anim/目录下。

``` xml
<set xmlns:android="http://schemas.android.com/apk/res/android"
	android:interpolator="@android:anim/decelerate_interpolator">
	<!-- 在1000ms（1秒）时间内，从透明度0（完全透明）变成1（不透明），同时由该元素中心位置从大小为0.1（十分之一）变成1（正常） -->
	<alpha
		android:fromAlpha="0.0"
		android:toAlpha="1.0"
		android:duration="1000" />
	<scale
		android:fromXScale="0.1"
		android:toXScale="1.0"
		android:fromYScale="0.1"
		android:toYScale="1.0"
		android:duration="1000"
		android:pivotX="50%"
		android:pivotY="50%"
		android:startOffSet="100" />
</set>
```

举例：(ImageView元素的id为m_image_view，上面的动画模板文件名为m_tween_animation)

``` java
public class MainActivity extends Activity {

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		ImageView mImageView = (ImageView) findViewById(R.id.m_image_view);
		Animation mAnimation = AnimationUtils.loadAnimation(this, R.drawable.m_tween_animation);
		mImageView.startAnimation(mAnimation);
	}

}
```
