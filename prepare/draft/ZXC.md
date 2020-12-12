RABBIT MQ 接口文档
http://127.0.0.1:15672/api/index.html

zookeeper(Apache) -> Shepher(Xiaomi)
redis -> nredis-proxy

MaxScale 

Fiddler抓包工具，抓取App数据

尝试用 XXX 方法实现……
优化……
修复在 XX 状态下对 XX 的异常处理

Java的运行依赖模板解释器和JIT编译器

DNS负载均衡 HTTP重定向和DNS解析
反向代理负载均衡 HTTP，请求转发
IP负载均衡(LVS-NAT) 传输层，修改发送来的IP数据包，将数据包的目标地址修改为实际服务器地址。
直接路由(LVS-DR) 数据链路层，修改数据包的目标MAC地址（没有修改目标IP），将数据包转发到实际服务器上，实际服务器的响应数据包将直接发送给客户羰，而不经过调度器。
IP隧道(LVS-TUN)


中间操作包括：map (mapToInt, flatMap 等)、 filter、distinct、sorted、peek、limit、skip、parallel、sequential、unordered。
终止操作包括：forEach、forEachOrdered、toArray、reduce、collect、min、max、count、anyMatch、allMatch、noneMatch、findFirst、findAny、iterator。

HandlerMethodReturnValueHandler 实现返回值的修改

select now();

select CURTIME();

show variables like "%time_zone%"

set global time_zone = '+8:00';
set time_zone = '+8:00';
flush privileges;


select * from information_schema.innodb_trx
show PROCESSLIST;
kill 6965067



反应式编程来源于数据流和变化的传播，意味着由底层的执行模型负责通过数据流来自动传播变化。

在计算机中，响应式编程或反应式编程（英语：Reactive programming）是一种面向数据流和变化传播的编程范式。

响应式编程（reactive programming）是一种基于数据流（data stream）和变化传递（propagation of change）的声明式（declarative）的编程范式。

Java的运行依赖模板解释器和JIT编译器

Flink
大数据，由 Java 代码实现，会把所有任务当成流来处理。
Client 用来提交任务给 JobManager，JobManager 分发任务给 TaskManager 去执行，然后 TaskManager 会心跳的汇报任务状态。



Storm
Webflux
Reactor 
https://blog.csdn.net/get_set/article/month/2018/03/2
http://www.jinxuliang.com/course2/java/JavaRxProgram/1_RxBasic/PPT.pdf


Base64 是一种将二进制数据表示为可打印字符的编码方法。
基本操作是将3个字节编码为4个 Base64 单元：3 * 8 bit = 4 * 6 bit. 
这种编码方法常用于处理文本数据的场合，例如在 HTML/XML 中表示、传输、存储一些二进制数据（如数据签名、数据校验等）。
Base64 编码后数据增长为原来的 4/3 ≈ 1.33 倍。相较于将二进制数据按照十六进制输出数据增长为原来2倍，Base64 更加节省空间。
Base64 根据编码字典表不同以及是否 padding (使用=作为 padding 字符)，对同一数据的编码结果可能不同。
使用最多的字典表有两个（区别主要是 62 和 63 使用的字符不同，[+与/]和[-与_]），所以表2是"URL and Filename safe"。


select @@innodb_autoinc_lock_mode;
默认值1，简单插入（可以确定插入行数）的时候，直接将auto_increment加1，而不会去锁表，提高了性能。
模式0的话就是不管什么情况都是加上表锁，等语句执行完成的时候在释放，如果真的添加了记录，将auto_increment加1。
模式2，什么情况都不加AUTO_INC锁，存在安全问题，当binlog格式设置为Statement模式的时候，从库同步的时候，执行结果可能跟主库不一致，问题很大。


A9B7C6
2B2B2B

Vavr 工具包 Function0 到 Function8。

Consumer<T>：接受一个输入，没有输出。抽象方法为 void accept(T t)。
Supplier<T>：没有输入，一个输出。抽象方法为 T get()。
Predicate<T>：接受一个输入，输出为 boolean 类型。抽象方法为 boolean test(T t)。
UnaryOperator<T>：接受一个输入，输出的类型与输入相同，相当于 Function<T, T>。
BinaryOperator<T>：接受两个类型相同的输入，输出的类型与输入相同，相当于 BiFunction<T,T,T>。
BiPredicate<T, U>：接受两个输入，输出为 boolean 类型。抽象方法为 boolean test(T t, U u)。

构成闭包的两个要件：一个函数；负责绑定自由变量的上下文环境。
闭包是用来封闭自由变量的，适合用来实现内部状态。

记忆化（memoization）也是函数式编程中的重要概念，其核心思想是以空间换时间，提高函数的执行性能，尤其是使用递归来实现的函数。

对于一个无序的流，可以使用 sorted 操作来排序；对于一个有序的流，可以使用 unordered() 方法来使其无序。

流操作可以是有状态或无状态的。当一个有状态的操作在处理一个元素时，它可能需要使用处理之前的元素时保留的信息；无状态的操作可以独立处理每个元素。

reduce 操作把一个流约简成单个结果。约简操作可以有 3 个部分组成：

初始值：在对元素为空的流进行约简操作时，返回值为初始值。
叠加器：接受 2 个参数的 BiFunction。第一个参数是当前的约简值，第二个参数是当前元素，返回结果是新的约简值。
合并器：对于【并行流】来说，约简操作可能在流的不同部分上并行执行。合并器用来把部分约简结果合并为最终的结果。
 

范畴（category）本身是一个很简单的概念。
一个范畴由对象（object）以及对象之间的箭头（arrow）组成。
范畴的核心是组合，体现在箭头的组合性上。如果从对象 A 到对象 B 有一个箭头，从对象 B 到对象 C 也有一个箭头，那么必然有一个从对象 A 到对象 C 的箭头。从 A 到 C 的这个箭头，就是 A 到 B 的箭头和 B 到 C 的箭头的组合。这种组合的必然存在性，是范畴的核心特征。
以专业术语来说，箭头被称为态射（morphisms）。

存储的数据，char数据类型的右侧空格存储的时候被删除了，但是左侧空格还保留。
char数据类型只是会判断查询条件中左侧的空格，右侧的空格也会忽略
存储的数据，varchar数据类型的空格均保留，没有进行删除。
查询的时候，varchar数据类型只是会判断查询条件中左侧的空格，会忽略右侧的空格
MySQL校对规则属于PADSPACE类。因此MySQL中的所有CHAR和VARCHAR值比较时不需要考虑任何尾部空格。


Lettuce是一个可扩展的线程安全的Redis客户端，提供同步，异步和响应式 API。
BLPOP、MULTI/EXEC可以共享一个连接。

Lettuce底层通过netty实现了多线程安全，即一个tcp连接可以同时处理多个redis操作。

Jedis不是线程安全的，故不应该在多线程环境中共用一个Jedis实例。但是，也应该避免直接创建多个Jedis实例，因为这种做法会导致创建过多的socket连接，性能不高。 
jedis操作redis的时候，对底层执行redis命令做了缓存，所以如果某一次redis操作出现异常，jedis实例中的缓存数据不会被清空，而直接放回连接池中。下一次从池中取出了同一个jedis对象，发送的命令用的还是上一个线程的数据。
jedis本身不是多线程安全的，这不是jedis的bug，而是jedis的设计与redis本身就是单线程相关。


获取IP地址
Enumeration allNetInterfaces = NetworkInterface.getNetworkInterfaces();
        InetAddress ip = null;
        while (allNetInterfaces.hasMoreElements()) {
            NetworkInterface netInterface = (NetworkInterface) allNetInterfaces.nextElement();
            System.out.println(netInterface.getName());
            Enumeration addresses = netInterface.getInetAddresses();
            while (addresses.hasMoreElements()) {
                ip = (InetAddress) addresses.nextElement();
                if (ip instanceof Inet4Address) {
                    System.out.println("本机的IP = " + ip.getHostAddress());
                }
            }
        }


Magent -》> Memcached -> Memcached plugin -> mySQL



    eureka-server服务注册与发现
    gateway zuul网关
    monitor-dashboard 服务调用监控看板
    netty-service netty实现的微服务示例
    the-service 简单微服务实现
    web-console WEB管理控制台
    uaa-service 账户与权限


忒坏代码
// Extract the IntegerCache through reflection
        Class<?> clazz = Class.forName("java.lang.Integer$IntegerCache");
        Field field = clazz.getDeclaredField("cache");
        field.setAccessible(true);
        Integer[] cache = (Integer[]) field.get(clazz);

        // Rewrite the Integer cache
        for (int i = 0; i < cache.length; i++) {
            cache[i] = new Integer(new Random().nextInt(cache.length));
        }

spring说什么，一般来说说都是java web相关，就是spring mvc，说dispatcher servlet，但是spring在我的理解里面就是ioc和aop

轻型目录访问协议（英文：Lightweight Directory Access Protocol，缩写：LDAP）是一个开放的，中立的，工业标准的应用协议，通过IP协议提供访问控制和维护分布式信息的目录信息。
OpenLDAP的服务器本身其实只相当于是一个mysql数据库。
安装phpLDAPadmin的工具，提供一个简单的图形界面
安装PWM，提供修改密码能力


对于 JVM 的调优，主要有两个方面考虑：
    内存大小配置
    垃圾回收算法选择


ArrayList的数组扩容，使用的是System.arrayCopy调用，native方法，在Java层面创建一个新的长度的数组，然后将老数组和新数组都传进去，在native里将老数组里的元素指针拷贝到新数组里，浅拷贝。
存在跨代引用的问题，因为传给System.arrayCopy的新数组是在Java层面构建传进来的，在新生代分配的可能性最大，并且是浅拷贝，那么老生代里的byte数组因为存在新生代里的引用，那仅仅做CMS GC就不可能回收这些老生代的对象了，因为CMS GC的一个gc root就是新生代里的对象。
只要保证在cms gc回收old之前做一次ygc就能保证新生代里的那个新数组被回收而没有指向老生代那些byte数组，那么这些数组就能正常被cms gc回收了，所以加上-XX:+CMSScavengeBeforeRemark即可解此问题。

﻿**[001]** TCP协议对应于传输层，而HTTP协议对应于应用层。http基于tcp协议。

**[002]** TCP/IP 协议分层模型

- 物理层

将二进制的0和1和电压高低，光的闪灭和电波的强弱信号进行转换

- 链路层

代表驱动

- 网络层

使用 IP 协议，IP 协议基于 IP 转发分包数据

IP 协议是个不可靠协议，不会重发

IP 协议发送失败会使用ICMP 协议通知失败

ARP 解析 IP 中的 MAC 地址，MAC 地址由网卡出厂提供
            
IP 还隐含链路层的功能，不管双方底层的链路层是啥，都能通信

- 传输层

通用的 TCP 和 UDP 协议

TCP 协议面向有连接，能正确处理丢包，传输顺序错乱的问题，但是为了建立与断开连接，需要至少7次的发包收包，资源浪费

UDP 面向无连接，不管对方有没有收到，如果要得到通知，需要通过应用层

传输层协议主要是为不同主机上的不同进程间提供了逻辑通信的功能。传输层只工作在端系统中。

多路复用与多路分解
将传输层报文段中的数据交付到正确的套接字的工作被称为多路分解。

在源主机上从不同的套接字中收集数据，封装头信息生成报文段后，将报文段传递到网络层，这个过程被称为多路复用。

无连接的多路复用和多路分解指的是 UDP 套接字的分配过程，一个 UDP 套接字由一个二元组来标识，这个二元组包含了一
个目的地址和一个目的端口号。因此不同源地址和端口号的 UDP 报文段到达主机后，如果它们拥有相同的目的地址和目的端
口号，那么不同的报文段将会转交到同一个 UDP 套接字中。

面向连接的多路复用和多路分解指的是 TCP 套接字的分配过程，一个 TCP 套接字由一个四元组来标识，这个四元组包含了
源 IP 地址、源端口号、目的地址和目的端口号。因此，一个 TCP 报文段从网络中到达一台主机上时，该主机使用全部 4 个
值来将报文段定向到相应的套接字。

UDP 协议
UDP 是一种无连接的，不可靠的传输层协议。它只提供了传输层需要实现的最低限度的功能，除了复用/分解功能和少量的差
错检测外，它几乎没有对 IP 增加其他的东西。UDP 协议适用于对实时性要求高的应用场景。

特点：

使用 UDP 时，在发送报文段之前，通信双方没有握手的过程，因此 UDP 被称为是无连接的传输层协议。因为没有握手
过程，相对于 TCP 来说，没有建立连接的时延。因为没有连接，所以不需要在端系统中保存连接的状态。
UDP 提供尽力而为的交付服务，也就是说 UDP 协议不保证数据的可靠交付。
UDP 没有拥塞控制和流量控制的机制，所以 UDP 报文段的发送速率没有限制。
因为一个 UDP 套接字只使用目的地址和目的端口来标识，所以 UDP 可以支持一对一、一对多、多对一和多对多的交互
通信。
UDP 首部小，只有 8 个字节。

UDP 报文段由首部和应用数据组成。报文段首部包含四个字段，分别是源端口号、目的端口号、长度和检验和，每个字段的长
度为两个字节。长度字段指的是整个报文段的长度，包含了首部和应用数据的大小。校验和是 UDP 提供的一种差错校验机制。
虽然提供了差错校验的机制，但是 UDP 对于差错的恢复无能为力。

- 会话层以上分层

TCP/IP 分层中，会话层，表示层，应用层集中在一起

网络管理通过 SNMP 协议

**[003]** TCP三次握手和四次挥手

TCP 协议
TCP 协议是面向连接的，提供可靠数据传输服务的传输层协议。

特点：

TCP 协议是面向连接的，在通信双方进行通信前，需要通过三次握手建立连接。它需要在端系统中维护双方连接的状态信息。
TCP 协议通过序号、确认号、定时重传、检验和等机制，来提供可靠的数据传输服务。
TCP 协议提供的是点对点的服务，即它是在单个发送方和单个接收方之间的连接。
TCP 协议提供的是全双工的服务，也就是说连接的双方的能够向对方发送和接收数据。
TCP 提供了拥塞控制机制，在网络拥塞的时候会控制发送数据的速率，有助于减少数据包的丢失和减轻网络中的拥塞程度。
TCP 提供了流量控制机制，保证了通信双方的发送和接收速率相同。如果接收方可接收的缓存很小时，发送方会降低发送
速率，避免因为缓存填满而造成的数据包的丢失。

TCP 报文段由首部和数据组成，它的首部一般为 20 个字节。

源端口和目的端口号用于报文段的多路复用和分解。

32 比特的序号和 32 比特的确认号，用与实现可靠数据运输服务。

16 比特的接收窗口字段用于实现流量控制，该字段表示接收方愿意接收的字节的数量。

4 比特的首部长度字段，该字段指示了以 32 比特的字为单位的 TCP 首部的长度。

6 比特的标志字段，ACK 字段用于指示确认序号的值是有效的，RST、SYN 和 FIN 比特用于连接建立和拆除。设置 PSH 字
段指示接收方应该立即将数据交给上层，URG 字段用来指示报文段里存在紧急的数据。

校验和提供了对数据的差错检测。

- 三次握手：

客户端–发送带有SYN标志的数据包–一次握手–服务端

服务端–发送带有SYN/ACK标志的数据包–二次握手–客户端

客户端–发送带有带有ACK标志的数据包–三次握手–服务端

- 四次挥手：因为 TCP 连接是全双工的，也就是说通信的双方都可以向对方发送和接收消息，所以断开连接需要双方的确认。

客户端-发送一个FIN，用来关闭客户端到服务器的数据传送

服务器-收到这个FIN，它发回一个ACK，确认序号为收到的序号加1 。和SYN一样，一个FIN将占用一个序号

服务器-关闭与客户端的连接，发送一个FIN给客户端

客户端-发回ACK报文确认，并将确认序号设置为收到序号加1

**[003.5]** ARQ
ARQ 协议指的是自动重传请求，它通过超时和重传来保证数据的可靠交付，它是 TCP 协议实现可靠数据传输的一个很重要的机制。

它分为停止等待 ARQ 协议和连续 ARQ 协议。

一、停止等待 ARQ 协议

停止等待 ARQ 协议的基本原理是，对于发送方来说发送方每发送一个分组，就为这个分组设置一个定时器。当发送分组的确认
回答返回了，则清除定时器，发送下一个分组。如果在规定的时间内没有收到已发送分组的肯定回答，则重新发送上一个分组。

对于接受方来说，每次接受到一个分组，就返回对这个分组的肯定应答，当收到冗余的分组时，就直接丢弃，并返回一个对冗余
分组的确认。当收到分组损坏的情况的时候，直接丢弃。

使用停止等待 ARQ 协议的缺点是每次发送分组必须等到分组确认后才能发送下一个分组，这样会造成信道的利用率过低。

**[004]** HTTP协议格式
HTTP的请求和响应的消息协议是一样的，分为三个部分，起始行、消息头和消息体。这三个部分以CRLF作为分隔符。

消息头部有很多键值对组成，多个键值对之间使用CRLF作为分隔符，也可以完全没有键值对。最后一个`消息头`有两个CRLF，用来表示消息头部的结束。

消息体是一个字符串，字符串的长度是由消息头部的Content-Length键指定的。如果没有Content-Length字段说明没有消息体，譬如GET请求就是没有消息体的，POST请求的消息体一般用来放置表单数据。GET请求的响应返回的页面内容也是放在消息体里面的。我们平时调用API返回的JSON内容都是放在消息体里面的。

HTTP请求的起始行称为请求行，形如GET /index.html HTTP/1.1

HTTP响应的起始行称为状态行，形如200 ok

**[005]** HTTP的无状态性
HTTP协议的无状态性是指服务器的协议层无需为不同的请求之间建立任何相关关系，它特指的是协议层的无状态性。

但是这并不代表建立在HTTP协议之上的应用程序就无法维持状态。应用层可以通过会话Session来跟踪用户请求之间的相关性，服务器会为每个会话对象绑定一个唯一的会话ID，浏览器可以将会话ID记录在本地缓存LocalStorage或者Cookie，在后续的请求都带上这个会话ID，服务器就可以为每个请求找到相应的会话状态。

**[006]** 输入url到页面加载都发生了什么事情

1. 输入地址
2. 浏览器查找域名的 IP 地址

- 这一步包括 DNS 具体的查找过程，包括：浏览器缓存->系统缓存->路由器缓存...

3. 浏览器向 web 服务器发送一个 HTTP 请求
4. 服务器的永久重定向响应（从 http://example.com 到 http://www.example.com）
5. 浏览器跟踪重定向地址
6. 服务器处理请求
7. 服务器返回一个 HTTP 响应
8. 浏览器显示 HTML
9. 浏览器发送请求获取嵌入在 HTML 中的资源（如图片、音频、视频、CSS、JS等等）
10. 浏览器发送异步请求



iText是著名的开放源码的站点sourceforge一个项目，是用于生成PDF文档的一个java类库。