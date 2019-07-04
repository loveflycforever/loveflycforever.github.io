---
layout: post
title:  "让振动器振动起来—— Vibrator 的使用"
tags: [Android]
comments: true
---
权限：

`<uses-permission  android:name="android.permission.VIBRATE"/>`

代码：
``` java
Vibrator mVibrator = (Vibrator) mContext.getSystemService(Service.VIBRATOR_SERVICE);
//前一个参数为设置震动周期数组，第二个参数为震动次数（-1表示只震动一次，为0则震动会一直持续）
//或者直接设置振动时长 mVibrator.vibrate(5000)
long[] pattern = {150, 100};
mVibrator.vibrate(pattern, -1);
//取消震动
mVibrator.cancel();
```
