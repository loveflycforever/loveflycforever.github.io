---
layout: post
title:  "禁止屏幕旋转并同时解决以至于导致 Activity 重启的方法"
date:   2015-10-14
tags: [Android]
comments: true
---
1.禁止屏幕旋转
在`AndroidManifest.xml`的每一个需要禁止转向的 Activity 配置中加入`android:screenOrientation`属性。
``` xml
//landscape（横向）portrait（纵向）
android:screenOrientation="landscape"
```

2.避免屏幕旋转时重启 Activity
Android 中每次切换屏幕方向时都会重启 Activity，所以应该在 Activity 销毁前保存当前活动的状态，在 Activity 再次 Create 的时候载入配置。
首先，在`AndroidManifest.xml`文件中重新定义方向(给每个需要的 Activity 加上`android:configChanges`属性)。
``` xml
<activity android:name="app.activity.MyActivity"
          android:label="@string/app_name"
          android:screenOrientation="landscape"
          android:configChanges="keyboardHidden|orientation">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```
同时，在需要的 Activity 中重写`onConfigurationChanged(Configuration newConfig)`方法。
``` java
@Override
public void onConfigurationChanged(Configuration newConfig) {
    super.onConfigurationChanged(newConfig);
 
    if(newConfig.orientation==Configuration.ORIENTATION_LANDSCAPE) {
        //横向
        setContentView(R.layout.file_list_landscape);
    } else {
        //纵向
        setContentView(R.layout.file_list);
    }
}
```
这样在屏幕旋转时就不会重启 Activity 了。
