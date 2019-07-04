---
layout: post
title:  "调节音量的各个方法——AudioManager的使用"
tags: [Android]
comments: true
---
AudioManager 类位于`android.Media`包中，该类提供访问控制音量和铃声模式的操作。

- 获取AudioManager实例对象

```
AudioManager audioManage = (AudioManager) context.getSystemService(Context.AUDIO_SERVICE);
```

- 获取最大音量和当前音量，参数：STREAM_VOICE_CALL（通话）、STREAM_SYSTEM（系统声音）、STREAM_RING（铃声）、STREAM_MUSIC（音乐）和STREAM_ALARM（闹铃）

```
int max = audioManager.getStreamMaxVolume(int streamType);
int current = audioManager.getStreamVolume(int streamType);
```

- 获取当前的铃声模式，返回值：RINGER_MODE_NORMAL（普通）、RINGER_MODE_SILENT（静音）或者RINGER_MODE_VIBRATE（震动）

```
int rMode = audioManager.getRingerMode();
```

- 获取当前音频模式，返回值：MODE_NORMAL（普通）、MODE_RINGTONE（铃声）、MODE_IN_CALL（呼叫）或者MODE_IN_COMMUNICATION（通话）

```
int mode = audioManager.getMode();
```

- 设置音量大小，第一个参数：STREAM_VOICE_CALL（通话）、STREAM_SYSTEM（系统声音）、STREAM_RING（铃声）、STREAM_MUSIC（音乐）和STREAM_ALARM（闹铃）；第二个参数：音量值，取值范围为0-7；第三个参数：可选标志位，用于显示出音量调节UI（AudioManager.FLAG_SHOW_UI）。

```
audioManager.setStreamVolume(int streamType, int index, int flags);
```

- 设置铃声模式，参数：RINGER_MODE_NORMAL（普通）、RINGER_MODE_SILENT（静音）或者RINGER_MODE_VIBRATE（震动）

```
audioManager.getRingerMode(int ringerMode);
```

- 设置音频模式，参数：MODE_NORMAL（普通）、MODE_RINGTONE（铃声）、MODE_IN_CALL（呼叫）或者MODE_IN_COMMUNICATION（通话）

```
audioManager.setMode(int mode);
```

- 设置静音/取消静音，第二个参数：请求静音状态，true（静音）false（取消静音）

```
audioManager.setStreamMute (int streamType, boolean state);
```

- 调节手机音量大小，第二个参数：调整音量的方向，可取ADJUST_LOWER（降低）、ADJUST_RAISE（升高）、ADJUST_SAME（不变）。

```
audioManager.adjustStreamVolume(int streamType, int direction, int flags);
```
