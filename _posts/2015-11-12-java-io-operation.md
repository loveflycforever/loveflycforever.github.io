---
layout: post
title:  "Java IO 操作"
date:   2015-11-12
tags: [Java, IO]
comments: true
---
1. 字节流
- 抽象类，InputStream 和 OutputStream
- 实现子类，FileInputStream 和 FileOutputStream
- 缓冲，BufferedInputStream 和 BufferedOutputStream

2. 字符流
- 抽象类，Writer 和 Reader
- 实现子类，FileWriter 和 FileReader
- 缓冲，BufferedWriter 和 BufferedReader

##### 字节流的读取写入
```java
FileInputStream i = null;
FileOutputStream o = null;
try {
    i = new FileInputStream(iPath);
    o = new FileOutputStream(oPath);

    byte[] buf = new byte[1024];
    // byte[] buf = new byte[i.available()]; // available()方法返回文件的字节数，如果文件过大，会内存溢出
    int temp = 0;
    while((temp = i.read(buf)) != -1) {
        o.write(buf, 0, temp);
    }
} catch (IOException e) {
    ...
} finally {
    ...
}
```

```java
BufferedInputStream bi = null;
BufferedOutputStream bo = null;
try {
    bi = new BufferedInputStream(new FileInputStream(iPath));
    bo = new BufferedOutputStream(new FileOutputStream(oPath));

    byte[] buf = new byte[1024];
    int temp = 0;
    while((temp = bi.read(buf)) != -1) {
        bo.write(buf,0,temp);
    }
} catch (IOException e) {
    ...
} finally {
    ...
}
```
##### 字符流的读取写入
```java
FileReader r = null;
FileWriter w = null;
try {
    r = new FileReader(rPath);
    w = new FileWriter(wPath);
    char[] buf = new char[1024];
    int temp = 0;
    while((temp = r.read(buf)) != -1) {
        w.write(new String(buf, 0, temp));
    }
} catch (IOException e) {
    ...
} finally {
    ...
}
```

```java
BufferedReader br = null;
BufferedWriter bw = null;
try {
    br = new BufferedReader(new FileReader(rPath));
    bw = new BufferedWriter(new FileWriter(wPath));
    String line = null;
    while((line = br.readLine()) != null) {
        bw.write(line);
        bw.newLine();
    }
} catch (IOException e) {
    ...
} finally {
    //不需要捕捉 FileReader 和 FileWriter 对象的异常，因为关闭缓冲区就是关闭缓冲区中的流对象。
    ...
}
```

字节流，用于操作无法直接获取文本信息的二进制文件，比如图片，mp3，视频文件等

字符流，用于操作类似文本文件或者带有字符的文件。

其实文件都是以字节存储的，只不过字符流在操作文本上面更方便。

缓冲区，能够在读写大文件的时候有效提高效率。
