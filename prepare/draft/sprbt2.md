Really Right Stuff RRS
Remote Radio Site

Fleet Attack Submarine FAS
Firsts And Seconds
Funny And Sweet
Faculty Arts Sciences
Frozen At Sea


柴门有庆、蓬屋生辉、陋荜生光

Jetty

要求Jetty最低版本为9.4。

Tomcat

要求Tomcat最低版本为8.5。

Hibernate

要求Hibernate最低版本为5.2。

---

java -javaagent:target/jvm-profiler-1.0.0.jar=reporter=com.uber.profiling.reporters.ConsoleOutputReporter,tag=mytag,metricInterval=5000,durationProfiling=com.uber.profiling.examples.HelloWorldApplication.publicSleepMethod,argumentProfiling=com.uber.profiling.examples.HelloWorldApplication.publicSleepMethod.1,sampleInterval=100 -cp target/jvm-profiler-1.0.0.jar com.uber.profiling.examples.HelloWorldApplication

Spring Boot 2.x

mvn spring-boot:run -Dspring-boot.run.jvmArguments="-javaagent:/opt/jvm-profiler/target/jvm-profiler-1.0.0.jar=reporter=com.uber.profiling.reporters.ConsoleOutputReporter,metricInterval=5000,durationProfiling=foo.bar.FooController.barMethod,sampleInterval=5000"

Spring Boot 1.x

mvn spring-boot:run -Drun.arguments="-javaagent:/opt/jvm-profiler/target/jvm-profiler-1.0.0.jar=reporter=com.uber.profiling.reporters.ConsoleOutputReporter,metricInterval=5000,durationProfiling=foo.bar.FooController.barMethod,sampleInterval=5000"

---


---

针对linux操作系统而言，将最高的1G字节（从虚拟地址0xC0000000到0xFFFFFFFF），供内核使用，称为内核空间，而将较低的3G字节（从虚拟地址0x00000000到0xBFFFFFFF），供各个进程使用，称为用户空间。

pcb 进程控制块

 最大传输单元（Maximum Transmission Unit，MTU）是指一种通信协议的某一层上面所能通过的最大数据包大小（以字节为单位）。

 TSS在任务切换过程中起着重要作用，通过它实现任务的挂起和恢复。所谓任务切换是指，挂起当前正在执行的任务，恢复或启动另一任务的执行。

基本思想是这样的：如果有多个应用程序需要同时访问同一块数据，那么可以为这些应用程序分配指向这块数据的指针，在每一个应用程序看来，它们都拥有这块数据的一份数据拷贝，当其中一个应用程序需要对自己的这份数据拷贝进行修改的时候，就需要将数据真正地拷贝到该应用程序的地址空间中去，也就是说，该应用程序拥有了一份真正的私有数据拷贝，这样做是为了避免该应用程序对这块数据做的更改被其他应用程序看到。这个过程对于应用程序来说是透明的，如果应用程序永远不会对所访问的这块数据进行任何更改，那么就永远不需要将数据拷贝到应用程序自己的地址空间中去。这也是写时复制的最主要的优点。

Netty 是一个基于 JAVA NIO 类库的异步通信框架，它的架构特点是：异步非阻塞、基于事件驱动、高性能、高可靠性和高可定制性。

1）阻塞、非阻塞的讨论对象是调用者；
2）同步、异步的讨论对象是被调用者。

---

Unsafe


单例、
实例获取方法 getUnsafe() 方法包含一个注释 @CallerSensitive，不是由系统类加载器（bootstrap classloader）加载，则将抛出 SecurityException
反射获取filed theUnsafe

allocateMemory、reallocateMemory、freeMemory 与 setMemory 分别是对 C 函数 malloc、realloc、free 和 memset 的封装，这样该类就提供了动态获取/释放本地方法区内存的功能。