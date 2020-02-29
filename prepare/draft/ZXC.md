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


iText是著名的开放源码的站点sourceforge一个项目，是用于生成PDF文档的一个java类库。