外多路复用 IO 为何比非阻塞 IO 模型的效率高是因为在非阻塞 IO 中，不断地询问 socket 状态
时通过用户线程去进行的，而在多路复用 IO 中，轮询每个 socket 状态是内核在进行的，这个效
率要比用户线程要高的多

1. BIO、NIO和AIO
BIO（同步阻塞IO）
在JDK1.4出来之前，建立网络连接的时候采用BIO模式，需要先在服务端启动一个ServerSocket，然后在客户端启动Socket来对服务端进行通信，
默认情况下服务端需要对每个请求建立一堆线程等待请求，而客户端发送请求后，先咨询服务端是否有线程相应，
如果没有则会一直等待或者遭到拒绝请求，如果有的话，客户端会线程会等待请求结束后才继续执行。
NIO（同步非阻塞IO）
NIO本身是基于事件驱动思想来完成的，其主要想解决的是BIO的大并发问题
BIO与NIO一个比较重要的不同，是我们使用BIO的时候往往会引入多线程，每个连接一个单独的线程；而NIO则是使用单线程或者只使用少量的多线程，每个连接共用一个线程。
NIO和IO最大的区别是数据打包和传输方式。IO是以流的方式处理数据，而NIO是以块的方式处理数据。
面向流的IO一次一个字节的处理数据，一个输入流产生一个字节，一个输出流就消费一个字节。为流式数据创建过滤器就变得非常容易，链接几个过滤器，以便对数据进行处理非常方便而简单，但是面向流的IO通常处理的很慢。
面向块的IO系统以块的形式处理数据。每一个操作都在一步中产生或消费一个数据块。按块要比按流快的多，但面向块的IO缺少了面向流IO所具有的优雅型和简单性
Buffer和Channel是标准NIO中的核心对象，网络NIO中还有个Selector核心对象
Channel是对原IO中流的模拟，任何来源和目的数据都必须通过一个Channel对象。一个Buffer实质上是一个容器对象，发给Channel的所有对象都必须先放到Buffer中；同样的，从Channel中读取的任何数据都要读到Buffer中。
Buffer是一个对象，它包含一些要写入或读出的数据。在NIO中，数据是放入buffer对象的，而在IO中，数据是直接写入或者读到Stream对象的。
应用程序不能直接对 Channel 进行读写操作，而必须通过 Buffer 来进行，即 Channel 是通过 Buffer 来读写数据的。
Channel是双向的，既可以读又可以写，而流是单向的
Channel可以进行异步的读写
对Channel的读写必须通过buffer对象
    FileChannel：从文件读取数据的
    DatagramChannel：读写UDP网络协议数据
    SocketChannel：读写TCP网络协议数据
    ServerSocketChannel：可以监听TCP连接
从文件中读取
FileInputStream fin = new FileInputStream( "readandshow.txt" );
FileChannel fc = fin.getChannel(); 
ByteBuffer buffer = ByteBuffer.allocate( 1024 );
fc.read( buffer );
写入数据到文件
FileOutputStream fout = new FileOutputStream( "writesomebytes.txt" );
FileChannel fc = fout.getChannel();
ByteBuffer buffer = ByteBuffer.allocate(1024);
for (int i=0; i<message.length; ++i) {
 buffer.put( message[i] );
}
buffer.flip();
fc.write(buffer);
读写结合
FileInputStream fi=new FileInputStream(new File(src));
FileOutputStream fo=new FileOutputStream(new File(dst));
//获得传输通道channel
FileChannel inChannel=fi.getChannel();
FileChannel outChannel=fo.getChannel();
//获得容器buffer
ByteBuffer buffer=ByteBuffer.allocate(1024);
while(true){
    //判断是否读完文件
    int eof =inChannel.read(buffer);
    if(eof==-1){
        break;  
    }
    //开始写
    //重设一下buffer的limit=position，position=0
    buffer.flip();
    outChannel.write(buffer);
    //写完要重置buffer，重设position=0,limit=capacity
    buffer.clear();
}
inChannel.close();
outChannel.close();
fi.close();
fo.close();

``` java
public final Buffer flip() {
        limit = position;
        position = 0;
        mark = -1;
        return this;
    }
```

Selector selector = Selector.open();
channel.configureBlocking(false);
SelectionKey key =channel.register(selector,SelectionKey.OP_READ);
AIO（异步阻塞IO）
与NIO不同，当进行读写操作时，只须直接调用API的read或write方法即可。这两种方法均为异步的，
对于读操作而言，当有流可读取时，操作系统会将可读的流传入read方法的缓冲区，并通知应用程序；
对于写操作而言，当操作系统将write方法传递的流写入完毕时，操作系统主动通知应用程序。 
在JDK1.7中，这部分内容被称作NIO.2，主要在java.nio.channels包下增加了下面四个异步通道：
AsynchronousSocketChannel
AsynchronousServerSocketChannel
AsynchronousFileChannel
AsynchronousDatagramChannel
同步和异步是针对应用程序和内核的交互而言的，
同步指的是用户进程触发IO操作并等待或者轮询的去查看IO操作是否就绪，而异步是指用户进程触发IO操作以后便开始做自己的事情，而当IO操作已经完成的时候会得到IO完成的通知。
阻塞和非阻塞是针对于进程在访问数据的时候，
根据IO操作的就绪状态来采取的不同方式，是一种读取或者写入操作函数的实现方式，阻塞方式下读取或者写入函数将一直等待，而非阻塞方式下，读取或者写入函数会立即返回一个状态值。
一般来说I/O模型可以分为：同步阻塞，同步非阻塞，异步阻塞，异步非阻塞IO
同步阻塞IO：
在此种方式下，用户进程在发起一个IO操作以后，必须等待IO操作的完成，只有当真正完成了IO操作以后，用户进程才能运行。JAVA传统的IO模型属于此种方式！
同步非阻塞IO:
在此种方式下，用户进程发起一个IO操作以后边可返回做其它事情，但是用户进程需要时不时的询问IO操作是否就绪，这就要求用户进程不停的去询问，从而引入不必要的CPU资源浪费。其中目前JAVA的NIO就属于同步非阻塞IO。
异步阻塞IO：
此种方式下是指应用发起一个IO操作以后，不等待内核IO操作的完成，等内核完成IO操作以后会通知应用程序，
这其实就是同步和异步最关键的区别，
同步必须等待或者主动的去询问IO是否完成，那么为什么说是阻塞的呢？因为此时是通过select系统调用来完成的，而select函数本身的实现方式是阻塞的，而采用select函数有个好处就是它可以同时监听多个文件句柄，从而提高系统的并发性！
异步非阻塞IO:
在此种模式下，用户进程只需要发起一个IO操作然后立即返回，等IO操作真正的完成以后，应用程序会得到IO操作完成的通知，此时用户进程只需要对数据进行处理就好了，不需要进行实际的IO读写操作，因为真正的IO读取或者写入操作已经由内核完成了。目前Java中还没有支持此种IO模型。   


单进程 ，上的 异步/同步，处理完一个请求，才能接收下一个请求。这就是同步。
上一个检查完了，再进行下一个检查，这就是 阻塞 的方式。

2. Netty 的各大组件
Bootstrap or ServerBootstrap
EventLoop
EventLoopGroup
ChannelPipeline
Channel
Future or ChannelFuture
ChannelInitializer
ChannelHandler
Bootstrap，一个Netty应用通常由一个Bootstrap开始，它主要作用是配置整个Netty程序，串联起各个组件。
Handler，为了支持各种协议和处理数据的方式，便诞生了Handler组件。Handler主要用来处理各种事件，这里的事件很广泛，比如可以是连接、数据接收、异常、数据转换等。
ChannelInboundHandler，一个最常用的Handler。这个Handler的作用就是处理接收到数据时的事件，也就是说，我们的业务逻辑一般就是写在这个Handler里面的，ChannelInboundHandler就是用来处理我们的核心业务逻辑。
ChannelInitializer，当一个链接建立时，我们需要知道怎么来接收或者发送数据，当然，我们有各种各样的Handler实现来处理它，那么ChannelInitializer便是用来配置这些Handler，它会提供一个ChannelPipeline，并把Handler加入到ChannelPipeline。
ChannelPipeline，一个Netty应用基于ChannelPipeline机制，这种机制需要依赖于EventLoop和EventLoopGroup，因为它们三个都和事件或者事件处理相关。
EventLoops的目的是为Channel处理IO操作，一个EventLoop可以为多个Channel服务。
EventLoopGroup会包含多个EventLoop。
Channel代表了一个Socket链接，或者其它和IO操作相关的组件，它和EventLoop一起用来参与IO处理。
Future，在Netty中所有的IO操作都是异步的，因此，你不能立刻得知消息是否被正确处理，但是我们可以过一会等它执行完成或者直接注册一个监听，具体的实现就是通过Future和ChannelFutures,他们可以注册一个监听，当操作执行成功或失败时监听会自动触发。总之，所有的操作都会返回一个ChannelFuture。
EventLoopGroup bossGroup = new NioEventLoopGroup();
EventLoopGroup workerGroup = new NioEventLoopGroup();
try { 
ServerBootstrap b = new ServerBootstrap(); 
b.group(bossGroup, workerGroup)
    .channel(NioServerSocketChannel.class)
    .option(ChannelOption.SO_BACKLOG, 100)
    .handler(new LoggingHandler(LogLevel.INFO))
    .childHandler(new ChannelInitializer<SocketChannel>() {
        @Override
        public void initChannel(SocketChannel ch) throws Exception {
           sc.pipeline().addLast(MarshallingCodeCFactory.buildMarshallingDecoder());
           sc.pipeline().addLast(MarshallingCodeCFactory.buildMarshallingEncoder());
           //超时handler（当服务器端与客户端在指定时间以上没有任何进行通信，则会关闭相应的通道，主要为减小服务端资源占用）
           sc.pipeline().addLast(new ReadTimeoutHandler(3)); 
        }
    }); 
ChannelFuture f = b.bind(port).sync();
f.channel().closeFuture().sync(); 
} finally {
    bossGroup.shutdownGracefully();
    workerGroup.shutdownGracefully();
} 
创建ServerBootstrap时，要传递两个NioEventLoopGroup线程池，一个叫bossGroup,一个叫workGroup。
bossGroup是用来处理TCP连接请求的，workGroup是来处理IO事件的。
bossGroup中有多个NioEventLoop线程，每个NioEventLoop绑定一个端口，也就是说，如果程序只需要监听1个端口的话，bossGroup里面只需要有一个NioEventLoop线程就行了。

3. Netty的线程模型
Netty是一个高性能、异步事件驱动的NIO框架，它提供了对TCP、UDP和文件传输的支持，作为一个异步NIO框架，Netty的所有IO操作都是异步非阻塞的，通过Future-Listener机制，用户可以方便的主动获取或者通过通知机制获得IO操作结果。
Netty基于NIO,实现了对NIO的封装及优化，从而Netty的通信模式为异步非阻塞通信
在IO编程过程中，当需要同时处理多个客户端接入请求时，可以利用多线程或者IO多路复用技术进行处理。IO多路复用技术通过把多个IO的阻塞复用到同一个select的阻塞上，从而使得系统在单线程的情况下可以同时处理多个客户端请求。
与传统的多线程/多进程模型比，I/O多路复用的最大优势是系统开销小，系统不需要创建新的额外进程或者线程，也不需要维护这些进程和线程的运行，降低了系统的维护工作量，节省了系统资源。
JDK1.4提供了对非阻塞IO（NIO）的支持，JDK1.6版本使用epoll替代了传统的select/poll，极大的提升了NIO通信的性能
    打开ServerSocketChannel
    绑定监听地址InetSocketAddress
    创建Selector，启动线程
    将ServerSocketChannel注册到Selector，监听SelectionKey.Op_Accept
    Selector轮训就绪的Key
    handleAccept（）处理新的客户端接入
    设置新建连接的Socket
    向Selector注册监听读操作SelectionKey.OP_READ
    handleRead（）异步读请求消息到ByteBuffer
    decode请求
    异步写ByteBuffer到SocketChannel
Reactor模式是事件驱动的，有一个或多个并发输入源，有一个Service Handler，有多个Request Handlers；这个Service Handler会同步的将输入的请求（Event）多路复用的分发给相应的Request Handler
从结构上，这有点类似生产者消费者模式，即有一个或多个生产者将事件放入一个Queue中，而一个或多个消费者主动的从这个Queue中Poll事件来处理；而Reactor模式则并没有Queue来做缓冲，每当一个Event输入到Service Handler之后，该Service Handler会立刻的根据不同的Event类型将其分发给对应的Request Handler来处理。
一个线程(单线程)来处理CONNECT事件(Acceptor)，一个线程池（多线程）来处理read,一个线程池（多线程）来处理write,那么从Reactor Thread到handler都是异步的，从而IO操作也多线程化。
Netty的高效并发编程主要体现在如下几点：
    volatile的大量、正确使用;
    CAS和原子类的广泛使用；
    线程安全容器的使用；
    通过读写锁提升并发性能。
Netty除了使用reactor来提升性能，当然还有
    零拷贝，IO性能优化
    通信上的粘包拆包
    同步的设计
    高性能的序列

4. TCP 粘包/拆包的原因及解决方法
tcp Header中有个Options标识位，常见的标识为mss(Maximum Segment Size)指的是，连接层每次传输的数据有个最大限制MTU(Maximum Transmission Unit)，一般是1500比特，超过这个量要分成多个报文段，mss则是这个最大限制减去TCP的header，光是要传输的数据的大小，一般为1460比特。换算成字节，也就是180多字节。
tcp为提高性能，发送端会将需要发送的数据发送到缓冲区，等待缓冲区满了之后，再将缓冲中的数据发送到接收方。同理，接收方也有缓冲区这样的机制，来接收数据。
发生TCP粘包、拆包主要是由于下面一些原因：
    应用程序写入的数据大于套接字缓冲区大小，这将会发生拆包。
    应用程序写入数据小于套接字缓冲区大小，网卡将应用多次写入的数据发送到网络上，这将会发生粘包。
    进行mss（最大报文长度）大小的TCP分段，当TCP报文长度-TCP头部长度>mss的时候将发生拆包。
    接收方法不及时读取套接字缓冲区数据，这将发生粘包。
使用如下方法解决：
    使用带消息头的协议、消息头存储消息开始标识及消息长度信息，服务端获取消息头的时候解析出消息长度，然后向后读取该长度的内容。
    设置定长消息，服务端每次读取既定长度的内容作为一条完整消息。
    设置消息边界，服务端从网络流中按消息编辑分离出消息内容。

5. 了解哪几种序列化协议？包括使用场景和如何去选择
    序列化（serialization）就是将对象序列化为二进制形式（字节数组），一般也将序列化称为编码（Encode），主要用于网络传输、数据持久化等；
    反序列化（deserialization）则是将从网络、磁盘等读取的字节数组还原成原始对象，以便后续业务的进行，一般也将反序列化称为解码（Decode），主要用于网络传输对象的解码，以便完成远程调用。

6. Netty的零拷贝实现
传统读操作
当应用发起一个从磁盘读取文件的操作时,请求会先经过内核,由内核与磁盘进行交互。数据会从磁盘拷贝到内核的缓存区中。这个copy动作由DMA（硬件和软件的信息传输,可以使用DMA(direct memory access)来完成）完成,整个过程中基本上不消耗CPU。
如果应用想拿到信息,还得从内核缓冲区获取,这里又存在一个cpu copy的动作,将数据从内核缓冲区中拷贝到应用缓冲区中。这个copy动作是需要消耗CPU的。
要想从磁盘中读取出内容,需要2次copy动作。
传统写操作
应用想将数据传递给客户端,必须经过内核,将数据先从应用缓冲区中copy(cpu copy)到内核的缓冲区中,对于写操作而言,通常这个内核缓冲区叫 kernel socket buffer
接着DMA会从kernel socket buffer中将数据拷贝(DMA copy)到protocol engine,最终将数据发送给客户端。
发生2次copy动作,一次是cpu copy,另外一次是DMA copy。
Java 类库通过 java.nio.channels.FileChannel 中的 transferTo() 方法来在 Linux 和 UNIX 系统上支持零拷贝。
为了响应客户端的请求,并把数据发送给客户端,服务端进行了两个核心操作read()、write()
在Linux中有一个mmap操作,可以减少拷贝次数,我们将read操作换成mmap操作
使用sendfile操作,减少一次上下文切换。
借助DMA引擎的gather操作，可以从多个不同的缓冲区中读取数据,DMA引擎是可以直接从kernel read buffer中读取数据的,而不用先将数据从kernel read buffer拷贝到kernel socket buffer后再进行读取操作。
到此,数据拷贝已经完全跟CPU没关系了,都是由内核操作的,内核只需要两次拷贝动作。
Netty 中的零拷贝完全是在用户态的，它的零拷贝的更多的是偏向于优化数据操作 。
    ByteBuf 的 slice 操作并不会拷贝一份新的 ByteBuf 内存空间，而是直接借用原来的 ByteBuf ，只是独立地保存读写索引。
    Netty 提供了 CompositeByteBuf 类，可以将多个 ByteBuf 组合成一个逻辑上的 ByteBuf 。
    Netty 的 FileRegion 中包装了 NIO 的 FileChannel.transferTo()方法，该方法在底层系统支持的情况下会调用 sendfile 方法，从而在传输文件时避免了用户态的内存拷贝。
    Netty 的 PooledDirectByteBuf 等类中封装了 NIO 的 DirectByteBuffer ，而 DirectByteBuffer 是直接在 jvm 堆外分配的内存，省去了堆外内存向堆内存拷贝的开销。
在基于流的传输过程（如TCP/IP）中，数据包有可能会被重新封装在不同的数据包中，而单个的数据包对应用而言是没有意义的，只有当这些数据包组成一条完整的消息时才能做出正确的处理。
为此我们可能会将这些零碎的数据包拼接成一个新的 ByteBuf 来处理，这其中无形之中会增加两次额外的数据拷贝操作了。
Netty 使用 CompositeByteBuf 类实现了将多个 ByteBuf 合并为一个逻辑上的 ByteBuf。
在 CompositeByteBuf 内部有一个包含Component 类型对象的list，而 Component 内部包含一个对 ByteBuf 对象的引用。
虽然看起来 CompositeByteBuf 是由多个 ByteBuf 组合而成的，不过在 CompositeByteBuf 内部，这些个 ByteBuf 都是单独存在的，CompositeByteBuf 只是逻辑上是一个整体，这样就避免了数据的拷贝，实现了零拷贝。
Netty 文件传输类 DefaultFileRegion 通过 transferTo 方法将文件发送至目标 Channel 中
Netty 使用了 DefaultFileRegion 来封装一个 FileChannel，然后就可以直接通过它将文件的内容直接写入 Channel 中，而不需要像传统方式拷贝文件内容到临时 buffer，然后再将 buffer 写入 Channel。

7. Netty的高性能表现在哪些方面
io线程模型 使用reactor模式，同步非阻塞。这决定了可以用最少的资源做更多的事。
内存零拷贝 使用直接缓存
内存池设计 申请的内存可以重用，主要指直接内存。 内部实现是用一颗二叉查找树管理内存分配情况。
串形化处理socket读写，避免锁，即一个指定socket的消息是串形化处理的。这样性能比多个线程同时 处理一个socket对应消息要好，因为多线程处理会有锁。
提供对protobuf等高性能序列化协议支持


























