---
layout: post
title:  "进行蓝牙连接的两种方式"
tags: [Android]
comments: true
---
为了在两台设备间创建一个连接，必须实现服务器端和客户端的机制，因为一个设备必须打开一个 Server Socket ，而另一个必须发起连接（使用服务器端设备的 MAC 地址发起连接）。

当服务器端和客户端在同一个 RFCOMM 信道上都有一个 BluetoothSocket 时，则两端就建立了连接。此刻，每个设备都能获得一个输入输出流，进行数据传输。

服务器端和客户端获得 BluetoothSocket 的方法是不同的，服务器端是在客户端的连接被接受时才产生一个 BluetoothSocket ，客户端是在打开一个到服务器端的 RFCOMM 信道时获得 BluetoothSocket 的。

蓝牙连接的一种实现技术是，每一个设备都自动准备作为一个服务器，所以每个设备都有一个 Server Socket 并监听连接。然后每个设备都能作为客户端建立一个到另一个设备的连接。

另外一种替代方法是，一个设备按需打开一个 Server Socket ，另外一个设备仅作为客户端建立与这个设备的连接。

#### 1. 作为服务器连接

如果要连接两个设备，其中一个必须充当服务器，它拥有 BluetoothServerSocket 。

服务器 Socket 的作用是侦听进来的连接，且在一个连接被接受时返回一个 BluetoothSocket 对象。

从 BluetoothServerSocket 获取到 BluetoothSocket 对象之后，BluetoothServerSocket 就可以（也应该）丢弃了，除非还要用它接收更多的连接。

下面是建立服务器 Socket 和接受一个连接的基本步骤：

- 步骤1 通过调用 listenUsingRfcommWithServiceRecord(String, UUID) 方法得到一个 BluetoothServerSocket 对象。字符串参数为服务的标识名称，名字是任意的，可以简单地是应用程序的名称。当客户端试图连接本设备时，它将携带一个 UUID 用来唯一标识它要连接的服务，UUID 必须匹配，连接才会被接受。

- 步骤2 通过调用 accept() 来侦听连接请求。这是一个阻塞线程，直到接受一个连接或者产生异常才会返回。当客户端携带的 UUID 与侦听它 Socket 注册的 UUID 匹配，连接请求才会被接受。如果成功，accept() 将返回一个 BluetoothSocket 对象。

- 步骤3 除非需要再接受另外的连接，否则的话调用close()。close()释放Server Socket及其资源，但不会关闭 accept() 返回的 BluetoothSocket 对象。与 TCP/IP 不同， RFCOMM 同一时刻一个信道只允许一个客户端连接，因此大多是情况下意味着在 BluetoothServerSocket 接受一个连接请求后应该立即调用 close() 。

accept() 调用不应该在主 Activity UI 线程中进行，因为它是个阻塞线程，会妨碍应用中其他的交互。通常在一个新线程中做 BluetoothServerSocket 或 BluetoothSocket 的所有工作来避免线程阻塞。如果需要放弃阻塞线程，可以调用 close() 方法。

下面是一个服务器组件接受连接的线程示例。
``` java
//定义接受线程
private class AcceptThread extends Thread {
	//创建BluetoothServerSocket类
	private final BluetoothServerSocket mmServerSocket;
	public AcceptThread() {
		BluetoothServerSocket tmp = null;
		try {
			//MY_UUID是应用的UUID标识
			tmp = mBluetoothAdapter.listenUsingRfcommWithServiceRecord(NAME, MY_UUID);
		} catch(IOException e) {}
		mmServerSocket = tmp;
	}
	//线程启动时候运行
	public void run() {
		BluetoothSocket socket = null;
		//保持侦听
		while(true) {
			try {
				//接受
				socket = mmServerSocket.accept();
			} catch(IOException e) {
				break;
			}
			//连接被接受
			if(socket!=null) {
				//管理连接
				manageConnectedSocket(socket);
				//关闭连接
				mmServerSocket.close();
				break;
			}
		}
	}
	//关闭连接
	public void cancel() {
		try
		{
			//关闭BluetoothServerSocket
			mmServerSocket.close();
		}
		catch(IOException e) {}
	}
}
```

本例中，只接受一个进来的连接，一旦连接被接受并获取 BluetoothSocket ，应用就发送获取到的 BluetoothSocket 给一个单独的线程，然后关闭 BluetoothServerSocket 并跳出循环。

**注意 accept() 返回 BluetoothSocket 后， Socket 就建立了连接，所以在客户端就不应该再调用 connect() 。**

#### 2.作为客户端连接

为了实现与远程服务器设备的连接，必须首先获得一个代表远程设备 BluetoothDevice 的对象。然后使用 BluetoothDevice 对象来获取一个 BluetoothSocket 以实现连接。

基本步骤如下：

- 步骤1 使用 BluetoothDevice 调用方法 createRfcommSocketToServiceRecord(UUID) 获取一个 BluetoothSocket 对象。

- 步骤2 调用 connect() 建立连接。当调用这个方法的时候，系统会在远程设备上完成一个 SDP 查找来匹配 UUID 。如果查找成功并且远程设备接受连接，就共享 RFCOMM 信道， connect() 会返回。这个方法也是一个阻塞的调用。如果连接失败或者超时（12秒）都会抛出异常。

**注意 要确保在调用 connect() 时没有同时做设备搜索，如果在搜索设备，该连接尝试会显著变慢，容易导致连接失败。**

下面是一个发起 Bluetooth 连接的线程示例。
``` java
//定义Bluetooth连接线程
private class ConnectThread extends Thread {
	//新建BluetoothSocket类
	private final BluetoothSocket mmSocket;
	//新建BluetoothDevice对象
	private final BluetoothDevice mmDevice;
	public ConnectThread(BluetoothDevice device) {
		BluetoothSocket tmp = null;
		//赋值给设备
		mmDevice = device;
		try {
			//根据UUID创建并返回一个BluetoothSocket
			tmp = device.createRfcommSocketToServiceRecord(MY_UUID);
		} catch(IOException e) {}
		//赋值给BluetoothSocket
		mmSocket = tmp;
	}
	public void run() {
		//取消发现设备
		mBluetoothAdapter.cancelDiscovery();
		try {
			//连接到设备
			mmSocket.connect();
		} catch(IOException connectException) {
			//无法连接，关闭Socket
			try
			{
				mmSocket.close();
			}
			catch (IOException connectException) {}
			return;
		}
		//管理连接
		manageConnectedSocket(mmSocket);
	}
	//取消连接
	public void cancel() {
		try
		{
			//关闭BluetoothSocket
			mmSocket.close();
		}
		catch(IOException e) {}
	}
}
```
**注意，cancelDiscovery() 应用连接操作前被调用。在连接之前，不管搜索有没有进行。该调用都是安全的，不需要确认（当然如果要确认，可以调用 isDiscovering() 方法）。处理完后别忘了调用 close() 方法来关闭连接的 Socket 和释放所有的内部资源。**

#### 3.管理连接

如果两个设备成功建立了连接，各自会有一个BluetoothSocket，此时可以在设备间共享数据了。使用BluetoothSocket，传输任何数据通常来说都比较容易，通常如下进行：

- 分别使用 getInputStream() 和 getOutputStream() 获取输入输出流来处理传输。
- 调用 read(byte[]) 和 write(byte[]) 来实现数据读写。

当然，要注意一些实现细节。比如，需要用一个专门的线程来实现流的读写，因为方法read(byte[])和write(byte[])都是阻塞调用。read(byte[])会阻塞，直到流中有数据可读。write(byte[])虽然通常不会阻塞，但是如果远程设备调用read(byte[])不够快而导致中间缓冲区满，它也可能阻塞。所以线程中的主循环应该用于读取InputStream。线程中也应该有单独的方法用来完成写OutputStream。
请看下面的示例：
``` java
//连接管理线程
private class ConnectThread extends Thread {
	//新建BluetoothSocket类
	private final BluetoothSocket mmSocket;
	//新建输入流对象
	private final InputStream mmInStream;
	//新建输出流对象
	private final OutputStream mmOutStream;
	public ConnectThread(BluetoothSocket socket) {
		//为BluetoothSocket赋初始值
		mmSocket = socket;
		//输入流赋值为null
		InputStream tmpIn = null;
		//输出流赋值为null
		OutputStream tmpOut = null;
		try {
			//从BluetoothSocket中获取输入流
			tmpIn = socket.getInputStream();
			//从BluetoothSocket中获取输出流
			tmpOut = socket.getOutputStream();
		} catch(IOException e) {}
		//为输入流赋值
		mmInStream = temIn;
		//为输出流赋值
		mmOutStream = temOut;
	}
	public void run() {
		//流的缓冲大小
		byte[] buffer = new byte[1024];
		//用于保存read()所读取的字节数
		int bytes;
		while(true) {
			try
			{
				//从输入流中读取数据
				bytes = mmInStream.read(buffer);
				//发送数据到界面
				mHandler.obtainMessage(MESSAGE_READ, bytes, -1, buffer).sendToTarget();
			}
			catch ()
			{
				break;
			}
		}
	}
	//发送数据到远程设备
	public void write(byte[] bytes) {
		try
		{
			//写数据到输出流中
			mmOutStream.write(bytes);
		}
		catch(IOException e) {}
	}
	//取消
	public void cancel() {
		try
		{
			//关闭B连接
			mmSocket.close();
		}
		catch(IOException e) {}
	}
}
```
构造函数中得到需要的流，一旦执行，线程会等待从InputStream来的数据，当read(byte[])返回从流中读到的字节后，数据通过父类的成员Handler被送到主Activity，然后继续等待读取流中的数据。向外发送数据只需简单地调用线程的write()方法。线程的cancel()方法是很重要的，以便连接可以在任何时候通过关闭BluetoothSocket来终止。它总在处理完Bluetooth连接后被调用。
