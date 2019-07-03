---
layout: post
title:  "文件访问"
date:   2019-05-29
excerpt: "文件访问"
tags: [文件]
comments: true
---
在Java IO中，流是一个核心的概念。流从概念上来说是一个连续的数据流。流与数据源或者数据流向的媒介相关联。在Java IO中流既可以是字节流(以字节为单位进行读写)，也可以是字符流(以字符为单位进行读写)。

Java I / O（输入和输出）用于处理输入并生成输出。

Java使用流的概念来快速进行I / O操作。java.io包中包含输入和输出操作所需的所有类。

我们可以通过Java I / O API 在Java中执行文件处理。

流
流是一系列数据。在Java中，流由字节组成。它被称为流，因为它就像一股水流继续流动。

在Java中，我们会自动为我们创建3个流。所有这些流都与控制台连接。

1）System.out：标准输出流

2）System.in：标准输入流

3）System.err：标准错误流

包java.io描述
通过数据流，序列化和文件系统提供系统输入和输出。除非另有说明，否则将null参数传递给此包中任何类或接口中的构造函数或方法将导致 抛出NullPointerException。

文件访问
网络访问
内存缓存访问
线程内部通信(管道)
缓冲
过滤
解析
读写文本 (Readers / Writers)
读写基本类型数据 (long, int etc.)
读写对象


一个程序需要InputStream或者Reader从数据源读取数据，需要OutputStream或者Writer将数据写入到目标媒介中。
InputStream和Reader与数据源相关联，OutputStream和writer与目标媒介相关联。

跳跃式地读取文件其中的某些部分，可以使用RandomAccessFile。

Java IO中的管道为运行在同一个JVM中的两个线程提供了通信的能力。所以管道也可以作为数据源以及目标媒介。

也可以使用两个管道共有的connect()方法使之相关联。PipedInputStream和PipedOutputStream都拥有一个可以互相关联的connect()方法。

当使用两个相关联的管道流时，务必将它们分配给不同的线程。read()方法和write()方法调用时会导致流阻塞，这意味着如果你尝试在一个线程中同时进行读和写，可能会导致线程死锁。

```
OutputStream output = new FileOutputStream("c:\\data\\system.out.txt");

PrintStream printOut = new PrintStream(output);

System.setOut(printOut);
```


PushbackInputStream 流的实现允许你将数据重新推回到流中，以便重新读取。


```
Reader reader = new FileReader("c:\\data\\myfile.txt");

int data = reader.read();

while(data != -1){

    char dataChar = (char) data;

    data = reader.read();

}
```

```
Reader reader = new InputStreamReader(inputStream);
Writer writer = new OutputStreamWriter(outputStream);
```

顺序读取
```
InputStream input1 = new FileInputStream("c:\\data\\file1.txt");

InputStream input2 = new FileInputStream("c:\\data\\file2.txt");

InputStream combined = new SequenceInputStream(input1, input2);

```

通过getLineNumber()方法获取当前行号，通过setLineNumber()方法设置当前行数(译者注：setLineNumber()仅仅改变LineNumberReader内的记录行号的变量值，不会改变当前流的读取位置。流的读取依然是顺序进行


```
LineNumberReader reader = new LineNumberReader(new FileReader("c:\\data\\input.txt"));

int data = reader.read();

while(data != -1){

    char dataChar = (char) data;

    data = reader.read();

    int lineNumber = reader.getLineNumber();

}
```

StreamTokenizer可以识别标示符，数字，引用的字符串，和多种注释类型。


标准的IO基于字节流和字符流进行操作的，而NIO是基于通道（Channel）和缓冲区（Buffer）进行操作，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。

Channel，Buffer 和 Selector 构成了核心的API。

使用Buffer读写数据一般遵循以下四个步骤：

	写入数据到Buffer
	调用flip()方法
	从Buffer中读取数据
	调用clear()方法或者compact()方法

调用clear()或compact()方法。clear()方法会清空整个缓冲区。compact()方法只会清除已经读过的数据。