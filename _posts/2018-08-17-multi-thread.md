---
layout: post
title:  "面试整理 多线程"
date:   2018-02-14
excerpt: "面试整理 多线程"
tags: ['面试']
comments: true
---
**[001]** 什么是线程？

进程是指一个内存中运行的应用程序，每个进程都有自己独立的一块内存空间，比如在Windows系统中，一个运行的exe就是一个进程。

线程是轻量级的进程，线程没有独立的地址空间(内存空间)，线程是由进程创建的(寄生在进程)。

一个进程中可以启动多个线程，就是常说的多线程编程。

线程的状态：
- 新建状态(new)
- 就绪状态(Runnable)
- 运行状态(Running)
- 阻塞状态(Blocked)
- 死亡状态(Dead)

当多个任务可以并行执行时，可以为每个任务启动一个线程，使用多线程。

Java中线程是指`java.lang.Thread`类的一个实例或线程的执行。使用`java.lang.Thread`类或`java.lang.Runnable`接口编写代码，定义、实例化、启动新线程。

线程分为两类：用户线程和守候线程。

当所有用户线程执行完毕后，JVM自动关闭。但是守候线程却独立于JVM，守候线程一般是由操作系统或用户自己创建的。

一般常见的Java应用程序都是单线程的。比如，用Java命令运行一个Java应用程序时，就启动了一个JVM进程，JVM找到程序程序的入口点`main()`，然后运行`main`方法，这样就产生了一个线程，这个线程称之为主线程。当`main`方法结束后，主线程运行完成。JVM进程也随即退出。

Java中每个线程都有一个调用栈，即使不在程序中创建任何新的线程，线程也在后台运行。`main`方法运行在一个线程内，称为主线程。一旦创建一个新的线程，就产生一个新的调用栈。

对于一个进程中的多个线程来说，多个线程共享进程的内存块，当有新的线程产生的时候，操作系统不分配新的内存，而是让新线程共享原有的进程块的内存。因此，线程间的通信很容易，速度也很快。不同的进程因为处于不同的内存块，因此进程之间的通信相对困难。

**[002]** 什么是线程安全和线程不安全？

存在成员变量的类应用于多线程时是不安全的，其不安全体现在这个成员变量可能发生非原子性的操作，而变量定义在方法内也就是局部变量是线程安全的。

Java线程的两个特性，可见性和有序性。 

多个线程之间是不能直接传递数据进行交互的，它们之间的交互只能通过共享变量来实现。

JVM有一个主内存，而每个线程有自己的工作内存，一个线程对一个变量进行操作时，都要在自己的工作内存里面建立一个拷贝，操作完之后再写入主内存。因此多个线程同时操作同一个变量，就可能会出现不可预知的结果。

线程安全就是说多线程访问同一代码，不会产生不确定的结果。编写线程安全的代码是依靠线程同步实现。

`synchronized`的关键字是建立一个监视器（monitor），这个监视器可以是要修改的变量，也可以其他被认为合适的对象或者方法，然后通过给这个监视器加锁来实现线程安全，每个线程在获得这个锁之后，要执行完`load in working memory －> use & assign －> store to main memory`的过程，才会释放它得到的锁。这样就实现了所谓的线程安全。

在多线程环境中，当各线程不共享数据的时候，那么一定是线程安全的。但是这种情况并不多见。

**[003]** 什么是自旋锁？

自旋锁是专为防止多处理器并发（实现保护共享资源）而引入的一种锁机制。

自旋锁与互斥锁比较类似，它们都是为了解决对某项资源的互斥使用。

对于互斥锁，如果资源已经被占用，资源申请者只能进入睡眠状态。

但是自旋锁不会引起调用者睡眠，如果自旋锁已经被别的执行单元保持，调用者就一直循环在那里看是否该自旋锁的保持者已经释放了锁，“自旋”一词就是因此而得名。

自旋锁的初衷就是：在短期间内进行轻量级的锁定。避免用户线程和内核的切换的消耗。

一个被争用的自旋锁使得请求它的线程在等待锁重新可用的期间进行自旋（有大量线程在竞争一个锁，需要消耗CPU，特别浪费处理器时间），所以自旋锁不应该被持有时间过长。

如果需要长时间锁定的话, 最好使用信号量。

自旋锁只有在内核可抢占或SMP（多处理器）的情况下才真正需要，在单CPU且不可抢占的内核下，自旋锁的所有操作都是空操作。

自旋锁是一种比较低级的保护数据结构或代码片段的方式，这种锁可能存在两个问题：死锁、过多占用CPU资源。

自旋锁比较适用于锁使用者保持锁时间比较短的情况。正是由于自旋锁使用者一般保持锁时间非常短，因此选择自旋而不是睡眠是非常必要的，自旋锁的效率远高于互斥锁。

信号量和读写信号量适合于保持时间较长的情况，因为它们会导致调用者睡眠，因此只能在进程上下文使用，而自旋锁适合于保持时间非常短的情况，它可以在任何上下文使用。

JVM对于自旋周期的选择，可能是50或者100次循环后就放弃，直接挂起线程，让出CPU资源，JDK5这个限度是固定写死的，在JDK6引入了适应性自旋锁，适应性自旋锁意味着自旋的时间不再是固定的了，而是由前一次在同一个锁上的自旋时间以及锁的拥有者（Owner）的状态来决定，基本认为一个线程上下文切换的时间是最佳的一个时间，同时JVM还针对当前CPU的负荷情况做了较多的优化。
- 如果平均负载小于CPU数量则一直自旋
- 如果有超过CPU总数量的一半个数（即CPU总数量的1.5倍）的线程正在自旋，则后来线程直接阻塞
- 如果正在自旋的线程发现拥有者发生了变化则延迟自旋时间（增加自旋计数）或进入阻塞
- 如果CPU处于节电模式则停止自旋
- 自旋时间的最坏情况是CPU的存储延迟（CPU A存储了一个数据到CPU B得知这个数据，之间的时间差）
- 自旋时会适当放弃线程优先级之间的差异

JDK6中`-XX:PreBlockSpin=10`为自旋次数；JDK7后，去掉此参数，由JVM控制。

示例
``` java
public class Test implements Runnable {
    static int sum;
    private SpinLock lock;
    
    public Test(SpinLock lock) {
        this.lock = lock;
    }
    
    public static void main(String[] args) throws InterruptedException {
        SpinLock lock = new SpinLock();
        for(int i = 0; i < 100; i++) {
            Test test = new Test(lock);
            Thread t = new Thread(test);
            t.start();
        }
        
        Thread.currentThread().sleep(1000);
        System.out.println(sum);
    }
    
    @Override
    public void run() {
        this.lock.lock();
        this.lock.lock();
        sum++;
        this.lock.unLock();
        this.lock.unLock();
    }
}
```
```
import java.util.concurrent.atomic.AtomicReference;
/**
 * 可重入的自旋锁（重新进入不会出现死锁）
 */
public class SpinLock {
    AtomicReference<Thread> owner = new AtomicReference<Thread>(); // 持有自旋锁的线程对象
    private int count; // 计数器，重入锁获取次数的计数
    public void lock() {
        Thread cur = Thread.currentThread();
        if(cur == owner.get()) { // 可重入的自旋锁
            count++;
            return;
        }
        
        while(!owner.compareAndSet(null, cur)) {
            // 当线程越来越多，由于while循环，会浪费CPU时间片，CompareAndSet需要多次对同一内存进行访问
            // 会造成内存的竞争，然而对于x86，会采取竞争内存总线的方式来访问内存，所以会造成内存访问速度下降，因而会影响整个系统的性能
        }
    }
    
    public void unLock() {
        Thread cur = Thread.currentThread();
        if (cur == owner.get()) {
            if (count > 0) {
                count--;
            } else {
                owner.compareAndSet(cur, null);
            }
        }
    }
}
```

**[004]** 什么是Java内存模型？

在并发编程领域，有两个关键问题：线程之间的通信和同步。

内存模型描述了程序中各个变量（实例域、静态域和数组元素）之间的关系，以及在实际计算机系统中将变量存储到内存和从内存中取出变量这样的底层细节。

对象最终是存储在内存里面的，但是编译器、运行库、处理器或者系统缓存可以有特权在变量指定内存位置存储或者取出变量的值。

在Java内存模型中，描述了在多线程代码中，哪些行为是正确的、合法的，以及多线程之间如何进行通信，代码中变量的读写行为如何反映到内存、CPU缓存的底层细节。

JMM允许编译器和缓存以数据在处理器特定的缓存（或寄存器）和主存之间移动的次序拥有重要的特权，除非程序员使用了`volatile`或`synchronized`明确了某些可见性的保证。

在Java中包含了几个关键字：`volatile`、`final`和`synchronized`，帮助程序员把代码中的并发需求描述给编译器。

Java内存模型中定义了它们的行为，确保正确同步的Java代码在所有的处理器架构上都能正确执行。

Java内存模型的两个关键概念：可见性（Visibility）和可排序性（Ordering）

Java语言规范中提到过，JVM中存在一个主存区（Main Memory或Java Heap Memory），Java中所有变量都是存在主存中的，对于所有线程进行共享，而每个线程又存在自己的工作内存（Working Memory），工作内存中保存的是主存中某些变量的拷贝，线程对所有变量的操作并非发生在主存区，而是发生在工作内存中，而线程之间是不能直接相互访问，变量在程序中的传递，是依赖主存来完成的。

而在多核处理器下，大部分数据存储在高速缓存中，如果高速缓存不经过内存的时候，也是不可见的一种表现。

在Java程序中，内存本身是比较昂贵的资源，其实不仅仅针对Java应用程序，对操作系统本身而言内存也属于昂贵资源，Java程序在性能开销过程中有几个比较典型的可控制的来源。

`synchronized`和`volatile`关键字提供的内存中模型的可见性保证，程序使用一个特殊的、存储关卡（memory barrier）的指令，来刷新缓存，使缓存无效，刷新硬件的写缓存并且延迟执行的传递过程，但是无疑该机制会对Java程序的性能产生一定的影响。

**[005]** 什么是CAS？

CAS（Compare and swap），比较并交换，就是实现平时所说的自旋锁或乐观锁的核心操作。解决的问题就是保证原子操作。

它的实现很简单，就是用一个预期的值和内存值进行比较，如果两个值相等，就用预期的值替换内存值，并返回 true。否则，返回 false。

用锁（`new ReentrantLock()`）或 `synchronized` 关键字可以实现原子操作，但是加锁或使用`synchronized`关键字带来的性能损耗较大（尽管JDK6为`synchronized`做了优化，增加了从偏向锁到轻量级锁再到重量级锁的过度，但是在最终转变为重量级锁之后，性能仍然较低），而用CAS可以实现乐观锁，它实际上是直接利用了CPU层面的指令，所以性能很高。

CAS是实现自旋锁的基础，CAS利用CPU指令保证了操作的原子性，以达到锁的效果，自旋，就是循环，一般是用一个无限循环实现。一个无限循环中，执行一个CAS操作，当操作成功，返回true时，循环结束；当返回false时，接着执行循环，继续尝试CAS操作，直到返回true。

Java中有许多地方用到了CAS,尤其是`java.util.concurrent`包下，比如`CountDownLatch`、`Semaphore`、`ReentrantLock`中，再比如`java.util.concurrent.atomic`包下，相信大家都用到过`Atomic*`，比如`AtomicBoolean`、`AtomicInteger`、`AtomicLong`。

`Unsafe`类存在于`sun.misc`包中，其内部方法操作可以像C语言的指针一样直接操作内存，单从名称看来就可以知道该类是非安全的，毕竟`Unsafe`类拥有着类似于C语言的指针操作，因此总是不应该首先使用`Unsafe`类，Java官方也不建议直接使用的`Unsafe`类，但还是很有必要了解该类，因为Java中CAS操作的执行依赖于`Unsafe`类的方法，注意`Unsafe`类中的所有方法都是`native`修饰的，也就是说`Unsafe`类中的方法都直接调用操作系统底层资源执行相应任务。

CAS适合简单对象的操作，比如布尔值、整型值等；

CAS适合冲突较少的情况，如果太多线程在同时自旋，那么长时间循环会导致CPU开销很大；

CAS是一种系统原语，原语属于操作系统用语范畴，是由若干条指令组成的，用于完成某个功能的一个过程，并且原语的执行必须是连续的，在执行过程中不允许被中断，也就是说CAS是一条CPU的原子指令，不会造成所谓的数据不一致问题。

CAS的ABA问题及其解决方案：

    假设这样一种场景，当第一个线程执行CAS(V,E,U)操作，在获取到当前变量V，准备修改为新值U前，另外两个线程已连续修改了两次变量V的值，使得该值又恢复为旧值，这样的话，就无法正确判断这个变量是否已被修改过。
    
    一般情况这种情况发现的概率比较小，可能发生了也不会造成什么问题，比如说我们对某个做加减法，不关心数字的过程，那么发生ABA问题也没啥关系。但是在某些情况下还是需要防止的，那么该如何解决呢？

在Java中解决ABA问题，我们可以使用以下两个原子类：

- `AtomicStampedReference`原子类是一个带有时间戳的对象引用，在每次修改后，`AtomicStampedReference`不仅会设置新值而且还会记录更改的时间。当`AtomicStampedReference`设置对象值时，对象值以及时间戳都必须满足期望值才能写入成功，这也就解决了反复读写时，无法预知值是否已被修改的窘境。

  底层实现为： 通过Pair私有内部类存储数据和时间戳, 并构造volatile修饰的私有实例，在更新时对数据和时间戳进行比较，只有两者都符合预期才会调用Unsafe的compareAndSwapObject方法执行数值和时间戳替换，也就避免了ABA的问题。

- `AtomicMarkableReference`维护的是一个布尔值的标识，也就是说至于true和false两种切换状态，这种方式并不能完全防止ABA问题的发生，只能减少ABA问题发生的概率。

**[006]** 什么是乐观锁和悲观锁？

锁从宏观上分类，分为悲观锁与乐观锁。

乐观锁是一种乐观思想，即认为读多写少，遇到并发写的可能性低，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，采取在写时先读出当前版本号，然后加锁操作（跟上一次的版本号比较，如果一样则更新），如果失败则要重复`读-比较-写`的操作。

Java中的乐观锁基本都是通过CAS操作实现的，CAS是一种更新的原子操作，比较当前值跟传入值是否一样，一样则更新，否则失败。

悲观锁是就是悲观思想，即认为写多，遇到并发写的可能性高，每次去拿数据的时候都认为别人会修改，所以每次在读写数据的时候都会上锁，这样别人想读写这个数据就会阻塞（block）直到拿到锁。

Java中的悲观锁就是`synchronized`，AQS框架下的锁则是先尝试CAS乐观锁去获取锁，如若获取不到，才会转换为悲观锁，如`RetreenLock`。

Java的线程是映射到操作系统原生线程之上的，如果要阻塞或唤醒一个线程就需要操作系统介入，需要在用户态与内核态之间切换，这种切换会消耗大量的系统资源，因为用户态与内核态都有各自专用的内存空间，专用的寄存器等，用户态切换至内核态需要传递给许多变量、参数给内核，内核也需要保护好用户态在切换时的一些寄存器值、变量等，以便内核态调用结束后切换回用户态继续工作。
- 如果线程状态切换是一个高频操作时，这将会消耗很多CPU处理时间；
- 如果对于那些需要同步的简单的代码块，获取锁挂起操作消耗的时间比用户代码执行的时间还要长，这种同步策略显然非常糟糕的。

`synchronized`会导致争用不到锁的线程进入阻塞状态，所以说它是Java语言中一个重量级的同步操纵，被称为重量级锁。

从JDK5开始，引入了轻量锁与偏向锁，默认启用了自旋锁，他们都属于乐观锁。

`markword`是Java对象数据结构中的一部分，`markword`数据的长度在32位和64位的虚拟机（未开启压缩指针）中分别为32bit和64bit，它的最后2bit是锁状态标志位，用来标记当前对象的状态，对象的所处的状态，决定了`markword`存储的内容。

**[007]** 什么是AQS？

`AbstractQueuedSynchronizer`，简称为AQS框架，是JDK5提供的一个基于FIFO等待队列实现的一个用于实现同步器的基础框架，这个基础框架的重要性可以这么说，JCU包里面几乎所有的有关锁、多线程并发以及线程同步器等重要组件的实现都是基于AQS这个框架。

Java内置的锁（使用同步方法和同步块）优势是可以花最小的空间开销创建锁（因为每个Java对象或者类都可以作为锁使用）和最少的时间开销获得锁（单线程可以在最短时间内获得锁）。然而，内置锁表现一般，而且不支持任何公平策略。从JDK5开始在`java.util.concurrent`包中引入了有别于`synchronized`的同步框架。

AQS的核心思想是基于`volatile int state`这样的一个属性，同时配合`Unsafe`工具对其原子性的操作，来实现对当前锁的状态进行修改。当`state`的值为0的时候，标识该锁没有被任何线程所占有。

AQS将线程封装到一个Node里面，并维护一个CHL Node FIFO队列，它是一个非阻塞的FIFO队列，也就是说在并发条件下往此队列做插入或移除操作不会阻塞，是通过自旋锁和CAS保证节点插入和移除的原子性，实现无锁快速插入。

因此，其实AQS主要就是维护了一个`state`属性、一个FIFO队列和线程的阻塞与解除阻塞操作。
- `state`表示同步状态，它的类型为32位整型，对state的更新必须要保证原子性。
- 队列是一个双向链表，每个节点里面都有一个prev和next，它们分别是前一个节点和后一个节点的引用。需要注意的是此双向链表除了链头其他每个节点内部都包含一个线程，而链头可以理解为一个空节点。

`ReentrantLock`的内部类`Sync`的父类就是`AbstractQueuedSynchronizer`（`Sync`的两个实现类分别是`NonfairSync`和`FairSync`，一个是用于实现公平锁、一个是用于实现非公平锁）。

`Sync`被设计成为内部类主要是为了安全性考虑。

AQS定义两种资源共享方式：`Exclusive`（独占，只有一个线程能执行，如`ReentrantLock`）和`Share`（共享，多个线程可同时执行，如`Semaphore`/`CountDownLatch`）。

**[008]** 什么是原子操作？在Java Concurrency API中有哪些原子类(atomic classes)？

原子操作：就是在执行某一操作时不被打断。

Linux原子操作问题来源于中断、进程的抢占以及多核SMP系统中程序的并发执行。

对于临界区的操作可以加锁来保证原子性，对于全局变量或静态变量操作则需要依赖于硬件平台的原子变量操作。

因此原子操作有两类：一类是各种临界区的锁，一类是操作原子变量的函数。

多核系统中的原子操作通常使用内存栅障（memory barrier）来实现，即一个CPU核在执行原子操作时，其他CPU核必须停止对内存操作或者不对指定的内存进行操作，这样才能避免数据竞争问题。

原子操作就是最小不可拆分的操作，也就是说操作一旦开始，就不能被打断，直至操作完成。在多线程环境下，原子操作是保证线程安全的重要手段。

原子操作是指一个不受其他操作影响的操作任务单元。原子操作是在多线程环境下避免数据不一致必须的手段。

例如，`int++`并不是一个原子操作，因为当一个线程读取它的值并加1时，另外一个线程有可能会读到之前的值，这就会引发错误。

JDK5，`java.util.concurrent.atomic`包提供了`int`和`long`类型的原子操作类，它们可以自动的保证对于他们的操作是原子的并且不需要使用同步。

**[009]** 什么是Executor框架？

从JDK5开始，增加了一个执行并行任务的框架——Executor框架。框架在`java.util.concurrent`包中。Executor框架实现了工作单元与执行单元的分离，使用Executor框架可以同步或异步地执行任务。

异步任务可以放在多线程中处理，但使用Executor框架比直接创建线程处理任务有很多好处，比如设置任务开始时间，取消任务队列，控制任务队列执行策略等，而且使用Executors可以很容易地创建线程池。

Executors是一个工厂类，可以方便的创建各种类型Executor或线程池。
``` java
Executor executor = Executors.newSingleThreadExecutor();  
```

- newSingleThreadExecutor()：创建一个Executor，顺序执行一个任务队列，每次只能执行一个任务。
- newFixedThreadPool(nThreads)：创建一个有固定数目线程的线程池，每次最多执行nThreads个任务，如果任务多于nThreads，多于的线程置于等待队列中
- newCachedThreadPool()：创建一个线程池，线程数目会随任务数目增加而增加，同时也会回收已经空闲的线程。
- newScheduledThreadPool(corePoolSize)：创建一个线程池，可以让任务延迟或周期性执行。

Executor有个缺点，一旦任务提交，就无法获得任务的执行情况，也无法停止，除非等待所有任务执行完毕或强制关闭JVM。

ExecutorService扩展了Executor接口，加入了生命周期管理。
``` java
ExecutorService pool = Executors.newFixedThreadPool(pool_size);
```

ExecutorService可以在运行时关闭，关闭后ExecutorService就停止接收新的任务。对已经接收到的任务有两种处理方法：
- 如果调用shutdown()方法，ExecutorService会等待所有接收到的任务执行完毕；
- 如果调用shutdownNow()方法，ExecutorService会取消所有等待的任务并尝试停止正在执行的任务。

ScheduledExecutorService扩展了ExecutorService接口，可以延迟或间期执行任务。
``` java
ScheduledExecutorService scheduleService = Executors.newScheduledThreadPool(pool_size);
```
- schedule(command, delay, TimeUnit): 任务延迟delay时间后执行
- scheduleAtFixedRate(command, initDelay, period, TimeUnit.SECONDS): 先延迟initDelay时间，然后每period时间执行一次任务
- scheduleWithFixedDelay(command, initDelay, delay, TimeUnit.SECONDS): 先延迟initDelay时间，然后执行任务，每次执行完毕后延迟delay时间再执行下次任务。

Executor主要由三部分组成：任务产生部分，任务处理部分，结果获取部分。（设计模式：生产者与消费者模式）

通过Future接口获取任务的结果
``` java
ExecutorService pool = Executors.newFixedThreadPool(3);
//CompletionService接口内部维护一个结果队列:一堆future....
CompletionService<Integer> cs = new ExecutorCompletionService<>(pool);
cs.submit(new Callable<Integer>() { @Override public Integer call() throws Exception { return flag; } });
cs.take().get()
```
    
**[010]** 什么是阻塞队列？如何使用阻塞队列来实现生产者-消费者模型？

阻塞队列是一个在队列基础上又支持了两个附加操作的队列。
- 支持阻塞的插入方法：队列满时，队列会阻塞插入元素的线程，直到队列不满。
- 支持阻塞的移除方法：队列空时，获取元素的线程会等待队列变为非空。

阻塞队列常用于生产者和消费者的场景，生产者是向队列里添加元素的线程，消费者是从队列里取元素的线程。

简而言之，阻塞队列是生产者用来存放元素、消费者获取元素的容器。

JDK7提供了7个阻塞队列
- ArrayBlockingQueue 数组结构组成的有界阻塞队列。按照先进先出（FIFO）的原则对元素进行排序，但是默认情况下不保证线程公平的访问队列。
- LinkedBlockingQueue 一个由链表结构组成的有界阻塞队列
- PriorityBlockingQueue 支持优先级的无界阻塞队列
- DelayQueue 支持延时获取元素的无界阻塞队列，即可以指定多久才能从队列中获取当前元素
- SynchronousQueue 不存储元素的阻塞队列，每一个put必须等待一个take操作，否则不能继续添加元素。并且他支持公平访问队列。
- LinkedTransferQueue 由链表结构组成的无界阻塞TransferQueue队列。
- LinkedBlockingDeque 链表结构的双向阻塞队列，优势在于多线程入队时，减少一半的竞争。

|方法\处理方式|抛出异常|返回特殊值|一直阻塞|超时退出
|:---|:---:|:---:|:---:|:---:|
|插入方法|add(e)|offer(e)|put(e)|offer(e,time,unit)|
|----
|移除方法|remove()|poll()|take()|poll(time,unit)|
|----
|检查方法|element()|peek()|不可用|不可用|
{: rules="groups"}

**[011]** 什么是Callable和Future?

Callable接口类似于Runnable，但是Runnable不会返回结果，并且无法抛出返回结果的异常，而Callable功能更强大一些，被线程执行后，可以返回值，这个返回值可以被Future拿到。
``` java
Callable<Integer> callable = new Callable<Integer>() {
    public Integer call() throws Exception {
        return new Random().nextInt(100);
    }
};
FutureTask<Integer> future = new FutureTask<Integer>(callable);
new Thread(future).start();
future.get()
```
或者
``` java
ExecutorService threadPool = Executors.newSingleThreadExecutor();
Future<Integer> future = threadPool.submit(new Callable<Integer>() {
    public Integer call() throws Exception {
        return new Random().nextInt(100);
    }
});
future.get()
```
多返回
``` java
ExecutorService threadPool = Executors.newCachedThreadPool();
CompletionService<Integer> cs = new ExecutorCompletionService<Integer>(threadPool);
for(int i = 1; i < 5; i++) {
    final int taskID = i;
    cs.submit(new Callable<Integer>() {
        public Integer call() throws Exception {
            return taskID;
        }
    });
}
for(int i = 1; i < 5; i++) {
    cs.take().get());
}
```

**[012]** 什么是FutureTask?

FutureTask类实现了RunnableFuture接口，而RunnableFuture继承了Runnable和Future接口
- 当Thread被start之后，会调用Thread的run方法
- 判断是否传入了Runnable类型的target，如果有，将会执行Runnable的run方法，这里传入的是FutureTask，所以会调用FutureTask类的run方法
- run方法调用了sync类的innerRun方法，sync为FutureTask的内部类
- innerRun方法中调用了callable的call方法，并将结果赋给result变量
- 而FutureTask的get方法，调用的是sync的innerGet方法，其返回了result变量

FutureTask 有两个很重要的属性 分别是 state、runner ，futureTask之所以可以支持cancel操作 就是因为这两个属性
|NEW|新建|0|
|----
|COMPLETING|执行中|1|
|----
|NORMAL|正常|2|
|----
|EXCEPTIONAL|异常|3|
|----
|CANCELLED|取消|4|
|----
|INTERRUPTING|中断中|5|
|----
|INTERRUNPED|被中断|6|
|----
{: rules="groups"}

state的状态变化可以有四种方式
- NEW->COMPLETING->NORMAL 正常完成的流程
- NEW->COMPLETING->EXCEPTIONAL 出现异常的流程
- NEW->CANCELED 被取消
- NEW->INTERRUNPING->INTERRRUNPTED 被中断

构造方法
``` java
public FutureTask(Runnable runnable, V result) {
        this.callable = Executors.callable(runnable, result);
        this.state = NEW;       // ensure visibility of callable
}
```
 
**[013]** 什么是同步容器和并发容器的实现？

同步容器可以简单的理解为通过`synchronized`来实现同步的容器，如果有多个线程调用同步容器的方法，它们将会串行执行。包括`Vector`和`Hashtable`，以及由同步容器封装类。`Collections.synchronized*`等工厂方法创建的类。

同步容器实现线程安全的方式将它们的状态封装起来，并对每个公有方法同步，使得每次只有一个线程能够访问容器的状态。

通过并发容器来代替同步容器，可以极大地提高伸缩性并降低风险。

**[014]** 什么是多线程？优缺点？

多线程的优点
- 无需跨进程边界
- 程序逻辑和控制方式简单
- 所有线程可以直接共享内存和变量等
- 线程方式消耗的总资源比进程方式好

多线程的缺点
- 每个线程与主程序共用地址空间，受限于地址空间
- 线程之间的同步和加锁控制比较麻烦
- 一个线程的崩溃可能影响到整个程序的稳定性
- 到达一定的线程数程度后，即使再增加CPU也无法提高性能，例如Windows Server 2003，大约是1500个左右的线程数就快到极限了（线程堆栈设定为1M），如果设定线程堆栈为2M，还达不到1500个线程总数
- 线程能够提高的总性能有限，而且线程多了之后，线程本身的调度也很麻烦，需要消耗较多的CPU

多进程的优点
- 每个进程互相独立，不影响主程序的稳定性，子进程崩溃没关系
- 通过增加CPU，就可以容易扩充性能
- 可以尽量减少线程加锁/解锁的影响，极大提高性能，就算是线程运行的模块算法效率低也没关系
- 每个子进程都有2GB地址空间和相关资源，总体能够达到的性能上限非常大

多进程的缺点
- 逻辑控制复杂，需要和主程序交互
- 需要跨进程边界，如果有大数据量传送，就不太好，适合小数据量传送、密集运算 多进程调度开销比较大

最好是多进程和多线程结合，即根据实际的需要，每个CPU开启一个子进程，这个子进程开启多线程可以为若干同类型的数据进行处理。

当然你也可以利用`多线程+多CPU+轮询`方式来解决问题……方法和手段是多样的，关键是自己看起来实现方便有能够满足要求，代价也合适。

进程的优点
- 顺序程序的特点：具有封闭性和可再现性
- 程序的并发执行和资源共享
- 多道程序设计出现后，实现了程序的并发执行和资源共享，提高了系统的效率和系统的资源利用率

进程的缺点
- 操作系统调度切换多个线程要比切换调度进程在速度上快的多
- 而且进程间内存无法共享，通讯也比较麻烦
- 线程之间由于共享进程内存空间，所以交换数据非常方便
- 在创建或撤消进程时，由于系统都要为之分配和回收资源，导致系统的开销明显大于创建或撤消线程时的开销  

线程的优点
- 它是一种非常"节俭"的多任务操作方式
- 在Linux系统下，启动一个新的进程必须分配给它独立的地址空间，建立众多的数据表来维护它的代码段、堆栈段和数据段，这是一种"昂贵"的多任务工作方式
- 而运行于一个进程中的多个线程，它们彼此之间使用相同的地址空间，共享大部分数据，启动一个线程所花费的空间远远小于启动一个进程所花费的空间
- 而且，线程间彼此切换所需的时间也远远小于进程间切换所需要的时间
- 当然，在具体的系统上，这个数据可能会有较大的区别
- 线程间方便的通信机制，由于同一进程下的线程之间共享数据空间，所以一个线程的数据可以直接为其它线程所用，这不仅快捷，而且方便
- 使多CPU系统更加有效。操作系统会保证当线程数不大于CPU数目时，不同的线程运行于不同的CPU上
- 改善程序结构。一个既长又复杂的进程可以考虑分为多个线程，成为几个独立或半独立的运行部分，这样的程序会利于理解和修改

线程的缺点
- 调度时, 要保存线程状态，频繁调度, 需要占用大量的机时； 程序设计上容易出错（线程同步问题）

多线程的主要优点
- 多线程技术使程序的响应速度更快 ,因为用户界面可以在进行其它工作的同时一直处于活动状态
- 当前没有进行处理的任务时可以将处理器时间让给其它任务
- 占用大量处理时间的任务可以定期将处理器时间让给其它任务
- 可以随时停止任务
- 可以分别设置各个任务的优先级以优化性能

是否需要创建多个线程取决于各种因素。在以下情况下,最适合采用多线程处理
- 耗时或大量占用处理器的任务阻塞用户界面操作
- 各个任务必须等待外部资源 (如远程文件或 Internet连接)

同样的 ,多线程也存在许多缺点 ,在考虑多线程时需要进行充分的考虑。

多线程的主要缺点包括
- 等候使用共享资源时造成程序的运行速度变慢。这些共享资源主要是独占性的资源 ,如打印机等
- 对线程进行管理要求额外的 CPU开销。线程的使用会给系统带来上下文切换的额外负担。当这种负担超过一定程度时,多线程的特点主要表现在其缺点上,比如用独立的线程来更新数组内每个元素
- 线程的死锁。即较长时间的等待或资源竞争以及死锁等多线程症状。
- 对公有变量的同时读或写。当多个线程需要对公有变量进行写操作时,后一个线程往往会修改掉前一个线程存放的数据,从而使前一个线程的参数被修改
- 当公用变量的读写操作是非原子性时,在不同的机器上,中断时间的不确定性,会导致数据在一个线程内的操作产生错误,从而产生莫名其妙的错误,而这种错误是程序员无法预知的

**[015]** 什么是多线程的上下文切换？

即使是单核的CPU也能够执行多线程，CPU通过给每个线程分配时间片来实现这个机制。时间片是CPU分配给各个线程的时间，因为时间非常短，所以CPU通过不断地切换线程，感觉多个线程是同时执行的，一般时间片为几十毫秒。

CPU通过时间片分配算法来循环执行任务，当前任务执行一个时间片后切换到下一个任务。但是，在切换前会保存上一个任务的状态，以便下次切换回这个任务时，可以再加载这个任务的状态。所以任务从保存到再加载的过程就是一次上下文切换。

多线程环境中，当一个线程由`Runnable`状态转换为非`Runnable`（`Blocked`、`Waiting`、`Timed Waiting`）状态时，相应线程的上下文信息（包括CPU的寄存器和程序计数器在某一时间点的内容等）需要被保存，以便相应线程稍后再次进入`Runnable`状态时能够在之前的执行进度的基础上继续前进。而一个线程从非`Runnable`状态进入`Runnable`状态可能涉及恢复之前保存的上下文信息。

这个对线程的上下文进行保存和恢复的过程就被称为上下文切换。

上下文切换会带来额外的开销，这包括对线程上下文信息保存和恢复的开销，对线程进行调度的CPU时间开销以及CPU缓存失效的开销。

**[016]** `ThreadLocal`的设计理念与作用？

`ThreadLocal`，线程局部变量。Java中的`ThreadLocal`类允许我们创建只能被同一个线程读写的变量。

它的实现原理其实比较简单，每个线程中都会维护一个`ThreadLocalMap`，当在某个线程中访问时，会取出这个线程自己的`Map`并且用当前`ThreadLocal`对象做索引来取出相对应的`Value`值，从而达到不同线程不同值的效果。

`ThreadLocalMap`里面存储的`Entry`对象本质上是一个WeakReference<ThreadLocal>。

因为线程的声明周期是长于`ThreadLocal`对象的，当此对象不再需要的时候如果线程中还持有它的引用势必也会产生内存泄漏的问题，所以自然应该是用弱引用来进行key的保存。

因此，如果一段代码含有一个`ThreadLocal`变量的引用，即使两个线程同时执行这段代码，它们也无法访问到对方的`ThreadLocal`变量。
```java
public static class MyRunnable implements Runnable {
    private ThreadLocal myThreadLocal = new ThreadLocal();
    public void run() {
        threadLocal.set((int) (Math.random() * 100D));
        threadLocal.get();
    }
}
```

两个线程分别执行`run()`方法，并且都在`ThreadLocal`实例上保存了不同的值。

如果它们访问的不是`ThreadLocal`对象并且调用的`set()`方法被同步了，则第二个线程会覆盖掉第一个线程设置的值。

但是，由于它们访问的是一个`ThreadLocal`对象，因此这两个线程都无法看到对方保存的值。也就是说，它们存取的是两个不同的值。

**[017]** ThreadPool（线程池）用法与优势？
1. 降低资源消耗。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
2. 提高响应速度。当任务到达时，任务可以不需要等到线程创建就能立即执行。
3. 提高线程的可管理性。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。
``` java
new ThreadPoolExecutor(corePoolSize, maximumPoolSize, keepAliveTime, milliseconds,runnableTaskQueue, handler);
```

`Executors.newCachedThreadPool`
创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程；

`Executors.newFixedThreadPool`
创建一个定长线程池，可控制线程最大并发数，超出的线程会在你队列中等待，线程池的大小一旦达到最大值就会保持不变，如果某个线程因为执行异常而结束，那么线程池会补充一个新线程。

`Executors.newScheduledThreadPool`
创建一个定长的线程池，支持定时即周期性任务执行

`Executors.newSingleThreadPool`
创建一个单线程化的线程池，它只会用唯一 的工作线程来执行任务，保证所有任务按照指定顺序（FIFO）执行，如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。此线程池保证所有任务的执行顺序按照任务的提交顺序执行

**[018]** Concurrent包里的其他东西：ArrayBlockingQueue、CountDownLatch等等

`Semaphore`：可以完成信号量控制，用来控制某个资源可被同时访问的个数，acquire()获取一个许可，如果没有就等待，而release()释放一个许可。比如在Windows下可以设置共享文件的最大客户端访问个数。 

`CountdownLatch`：典型的应用场景是，有一个任务想要往下执行，但必须要等到其他的任务执行完毕后才可以继续往下执行。

`CyclicBarrier`：类有一个整数初始值，此值表示将在同一点同步的线程数量。

CountDownLatch是减计数方式，计数==0时释放所有等待的线程；CyclicBarrier是加计数方式，计数达到构造方法中参数指定的值时释放所有等待的线程。

**[019]** `synchronized`和`ReentrantLock`的区别？

最大区别就是对于synchronized来说，它是Java语言的关键字，是原生语法层面的互斥，需要JVM实现。而ReentrantLock它是JDK5之后提供的API层面的互斥锁，需要lock()和unlock()方法配合try/finally语句块来完成。

在JDK5之前都是使用`synchronized`关键字保证同步的，它可以把任意一个非null的对象当作锁。
- 作用于方法时，锁住的是对象的实例（this）
- 作用于静态方法时，锁住的是类实例，又因为类的相关数据存储在永久带PermGen（JDK8则是metaspace），永久带是全局共享的，因此静态方法锁相当于类的一个全局锁，会锁所有调用该方法的线程
- 作用于一个对象实例时，锁住的是所有以该对象为锁的代码块

Synchronized的实现
- `Waiting Queue``
- `Contention List` 竞争队列，所有请求锁的线程首先被放在这个竞争队列中
- `Entry List` 处于Contention List中那些有资格成为候选资源的线程将被移动到Entry List中
- `Ready Thread`
- `OnDeck` 任意时刻，最多只有一个线程正在竞争锁资源，该线程被成为OnDeck
- `Running Thread`
- `Owner` 当前已经获取到所资源的线程被称为Owner
- `Blocking Queue`
- `WaitSet` 哪些调用wait方法被阻塞的线程被放置在这里

JVM每次从队列的尾部取出一个数据用于锁竞争候选者（OnDeck），但是并发情况下，Contention List会被大量的并发线程进行CAS访问，为了降低对尾部元素的竞争，JVM会将一部分线程移动到Entry List中作为候选竞争线程。

Owner线程会在unlock时，将Contention List中的部分线程迁移到Entry List中，并指定Entry List中的某个线程为OnDeck线程（一般是最先进去的那个线程）。Owner线程并不直接把锁传递给OnDeck线程，而是把锁竞争的权利交给OnDeck，OnDeck需要重新竞争锁。这样虽然牺牲了一些公平性，但是能极大的提升系统的吞吐量，在JVM中，也把这种选择行为称之为“竞争切换”。

OnDeck线程获取到锁资源后会变为Owner线程，而没有得到锁资源的仍然停留在Entry List中。如果Owner线程被wait方法阻塞，则转移到WaitSet队列中，直到某个时刻通过notify或者notifyAll唤醒，会重新进去EntryList中。

处于Contention List、Entry List、WaitSet中的线程都处于阻塞状态，该阻塞是由操作系统来完成的（Linux内核下采用pthread_mutex_lock内核函数实现的）。

synchronized是非公平锁。synchronized在线程进入ContentionList时，等待的线程会先尝试自旋获取锁，如果获取不到就进入ContentionList，这明显对于已经进入队列的线程是不公平的，还有一个不公平的事情就是自旋获取锁的线程还可能直接抢占OnDeck线程的锁资源。

区别一，使用方式
``` java
// synchronized的使用
public synchronized void test() {}
synchronized（Object） {}
// ReentrantLock的使用
private Lock lock = new ReentrantLock();
try{lock.lock();}
finally{ lock.unlock();}
```

区别二，使用`synchronized`，如果Thread1不释放，Thread2将一直等待，不能被中断，使用ReentrantLock，可以中断等待，转而去做别的事情。

区别三，synchronized的锁是非公平锁，ReentrantLock默认情况下也是非公平锁，但可以通过带布尔值的构造函数要求使用公平锁。
  公平锁是指多个线程在等待同一个锁时，必须按照申请的时间顺序来依次获得锁；而非公平锁则不能保证这一点。非公平锁在锁被释放时，任何一个等待锁的线程都有机会获得锁。

区别四，ReentrantLock可以同时绑定多个Condition对象，只需多次调用newCondition方法即可。synchronized中，锁对象的wait()和notify()或notifyAll()方法可以实现一个隐含的条件。但如果要和多于一个的条件关联的时候，就不得不额外添加一个锁。

区别五，JDK5中，synchronized还有很大的优化余地。JDK6中加入了很多针对锁的优化措施，synchronized与ReentrantLock性能方面基本持平。虚拟机在未来的改进中更偏向于原生的synchronized。

其他区别
- Java中每个对象都有一个锁（lock）或者叫做监视器（monitor）。
- ReentrantLock和synchronized持有的对象监视器不同。
- 如果某个synchronized方法是static的，那么当线程方法改方法时，它锁的并不是synchronized方法所在的对象，而是synchronized方法所在对象所对应的Class对象
- synchronized 方法是一种粗粒度的并发控制，某一时刻，只能有一个线程执行synchronized方法；
- synchronized块则是一种细粒度的并发控制。只会将块中代码同步，位于方法内，synchronized块之外的代码是可以被多个线程同时访问的。
- synchronized关键字经过编译之后，会在同步块的前后分别形成monitorenter和monitorexit两个字节码指令，操作对象均为锁的计数器。 
- 都是可重入的。可重入值的是同一个线程多次试图获取它所占的锁，请求会成功。当释放的时候，直到冲入次数清零，锁才释放。
    
**[020]** Semaphore有什么作用？

Semaphore是一个线程同步的辅助类，可以维护当前访问自身的线程个数，并提供了同步机制。可以用来控制同时访问资源的线程个数，例如，实现一个文件允许的并发访问数。

单个信号量的Semaphore对象可以实现互斥锁的功能，并且可以是由一个线程获得了“锁”，再由另一个线程释放“锁”，这可应用于死锁恢复的一些场合。

``` java
ExecutorService service = Executors.newCachedThreadPool();
final  Semaphore sp = new Semaphore(3); // 创建Semaphore信号量，初始化许可大小为3
Runnable runnable = new Runnable(){
    public void run(){
        sp.acquire(); // 请求获得许可，如果有可获得的许可则继续往下执行，许可数减1。否则进入阻塞状态
        Thread.sleep((long)(Math.random()*10000));
        sp.release(); // 释放许可，许可数加1
    }
});
service.execute(runnable);   
```

**[021]** Java Concurrency API中的Lock接口（Lock interface）是什么？对比同步它有什么优势？

```java
public interface Lock {
    void lock(); // 获取锁
    void lockInterruptibly() throws InterruptedException; // 可中断的获取锁
    boolean tryLock(); // 尝试非阻塞的获取锁
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException; // 超时获取锁
    void unlock(); // 释放锁
    Condition newCondition(); // 获取等待通知组件，和当前的锁绑定
}
```

当使用Lock时，获取锁和释放锁都是主动调用执行的，而synchronized则是系统自动释放锁的。

``` java
Lock lock = new ReentrantLock(); // 可重入锁（Lock的一种实现）
lock.lock();
try{
    dosomething();
}finally{
    lock.unlock();
}
```

不要将获取锁——lock()放在try中，如果在获取锁时发生了异常，异常抛出的同时，也会导致锁的无故释放（需要主动释放）。

在资源竞争不是很激烈的情况下，synchronized的性能要优于Lock；但是在资源竞争很激烈的情况下，synchronized的性能会下降几十倍，Lock的性能更优；
- Lock是一个接口，是代码层面的实现；synchronized是关键字，是内置的语言实现（JVM层面）。
- Lock是显示地获取释放锁，扩展性更强；synchronized是隐式地获取释放锁，更简捷。
- Lock在发生异常时，如果没有主动通过unlock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生。
- Lock可以让等待锁的线程响应中断；而使用synchronized时等待锁的线程会一直等待下去，不能响应中断；
- Lock可以尝试非阻塞、可中断、超时地获取锁；synchronized不可以。
- Lock可以知道是否成功获取锁；synchronized无法知道。

**[022]** Hashtable的size()方法中明明只有一条语句`return count`，为什么还要做同步？    
Java代码最终是被翻译成汇编代码执行的，汇编代码才是真正可以和硬件电路交互的代码。即使你看到Java代码只有一行，甚至你看到Java代码编译之后生成的字节码也只有一行，也不意味着对于底层来说这句语句的操作只有一个。
`return count`假设被翻译成了三句汇编语句执行，可能执行完第一句，线程就切换了。
    
**[023]** ConcurrentHashMap的并发度是什么？

并发度可以理解为程序运行时能够同时更新ConccurentHashMap且不产生锁竞争的最大线程数，实际上就是ConcurrentHashMap中的分段锁个数，即Segment[]的数组长度。

因此，Segment的大小也被称为ConcurrentHashMap的并发度。

ConcurrentHashMap，它内部细分了若干个小的HashMap，称之为段(Segment)。默认情况下一个ConcurrentHashMap被进一步细分为16个段。

当用户设置并发度时，ConcurrentHashMap会使用大于等于该值的最小2幂指数作为实际并发度（假如用户设置并发度为17，实际并发度则为32）。

运行时通过将key的高n位（n = 32 – segmentShift）和并发度减1（segmentMask）做位与运算定位到所在的Segment。

segmentShift与segmentMask都是在构造过程中根据concurrency level被相应的计算出来。

如果并发度设置的过小，会带来严重的锁竞争问题；

如果并发度设置的过大，原本位于同一个Segment内的访问会扩散到不同的Segment中，CPU cache命中率会下降，从而引起程序性能下降。

（文档的说法是根据你并发的线程数量决定，太多会导性能降低）

若没有则添加：putIfAbsent()，替换：replace()

**[024]** ReentrantReadWriteLock读写锁的使用？

对象的方法中一旦加入synchronized修饰，则任何时刻只能有一个线程访问synchronized修饰的方法。

假设有个数据对象拥有写方法与读方法，多线程环境中要想保证数据的安全，需对该对象的读写方法都要加入synchronized同步块。

这样任何线程在写入时，其它线程无法读取与改变数据；如果有线程在读取时，其他线程也无法读取或写入。

这种方式在写入操作远大于读操作时，问题不大，而当读取远远大于写入时，会造成性能瓶颈，因为此种情况下读取操作是可以同时进行的，而加锁操作限制了数据的并发读取。  

ReadWriteLock解决了这个问题，当写操作时，其他线程无法读取或写入数据，而当读操作时，其它线程无法写入数据，但却可以读取数据 。

ReentrantReadWriteLock与ReentrantLock彼此之间没有继承或实现的关系，是单独的实现。
``` java
private ReentrantReadWriteLock rwl = new ReentrantReadWriteLock();
rwl.readLock().lock(); // 上读锁，其他线程只能读不能写
rwl.readLock().unlock(); // 释放读锁，最好放在finnaly里面
rwl.writeLock().lock(); // 上写锁，不允许其他线程读也不允许写
rwl.writeLock().unlock(); // 释放写锁  
```
对于ReentantReadWriteLock因为要维护两个锁（读/写），但是同步状态state只有一个，所以ReentantReadWriteLock采用“按位切割”的方式

所谓“按位切割”就是将这个32位的int型state变量分为高16位和低16位来使用，高16位代表读状态，低16位代表写状态。

锁降级指的是先获取到写锁，然后获取到读锁，然后释放了写锁的过程。 

**[025]** CyclicBarrier和CountDownLatch的用法及区别？

CyclicBarrier和CountDownLatch 都位于java.util.concurrent 这个包下

|CountDownLatch|CyclicBarrier|
|----
|减计数方式|加计数方式|
|----
|计算为0时释放所有等待的线程|计数达到指定值时释放所有等待线程|
|----
|计数为0时，无法重置|计数达到指定值时，计数置为0重新开始|
|----
|调用countDown()方法计数减一，调用await()方法只进行阻塞，对计数没任何影响|调用await()方法计数加1，若加1后的值不等于构造方法的值，则线程阻塞|
|----
|不可重复利用|可重复利用|
|----
{: rules="groups"}

CountDownLatch方法
- public CountDownLatch(int count) {} // 参数count为计数值
- public void await() throws InterruptedException {} // 调用await()方法的线程会被挂起，它会等待直到count值为0才继续执行
- public boolean await(long timeout, TimeUnit unit) throws InterruptedException {} // 和await()类似，只不过等待一定的时间后count值还没变为0的话就会继续执行
- public void countDown() {} // 将count值减1


``` java
public class countDownlatchTest {
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(5);
        for(int i=0;i<5;i++){
            new Thread(new readNum(i,countDownLatch)).start();
        }
        countDownLatch.await();
    }
 
    static class readNum  implements Runnable{
        private int id;
        private CountDownLatch latch;
        public readNum(int id,CountDownLatch latch){
            this.id = id;
            this.latch = latch;
        }
        @Override
        public void run() {
            synchronized (this){
                latch.countDown();
            }
        }
    }
}
```

CyclicBarrier方法
- public CyclicBarrier(int parties, Runnable barrierAction) {} // 参数parties指让多少个线程或者任务等待至barrier状态；参数barrierAction为当这些线程都达到barrier状态时会执行的内容。 
- public CyclicBarrier(int parties) {}
- public int await() throws InterruptedException, BrokenBarrierException {} // 挂起当前线程，直至所有线程都到达barrier状态再同时执行后续任务；
- public int await(long timeout, TimeUnit unit)throws InterruptedException,BrokenBarrierException,TimeoutException {} // 让这些线程等待至一定的时间，如果还有线程没有到达barrier状态就直接让到达barrier的线程执行后续任务

``` java
public class cyclicBarrierTest {
    public static void main(String[] args) throws InterruptedException {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(5, new Runnable() {
            @Override
            public void run() {
                System.out.println("线程组执行结束");
            }
        });
        for (int i = 0; i < 5; i++) {
            new Thread(new readNum(i,cyclicBarrier)).start();
        }
        // CyclicBarrier 可以重复利用，这个是CountDownLatch做不到的
//        for (int i = 11; i < 16; i++) {
//            new Thread(new readNum(i,cyclicBarrier)).start();
//        }
    }
    static class readNum  implements Runnable{
        private int id;
        private CyclicBarrier cyc;
        public readNum(int id,CyclicBarrier cyc){
            this.id = id;
            this.cyc = cyc;
        }
        @Override
        public void run() {
            synchronized (this){
                System.out.println("id:"+id);
                try {
                    cyc.await();
                    System.out.println("线程组任务" + id + "结束，其他任务继续");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

**[026]** LockSupport工具？

LockSupport是JDK中比较底层的类，用来创建锁和其他同步工具类的基本线程阻塞原语。

Java锁和同步器框架的核心AQS（AbstractQueuedSynchronizer），就是通过调用LockSupport.park()和LockSupport.unpark()实现线程的阻塞和唤醒的。 

LockSupport类似于二元信号量（只有1个许可证可供使用），如果这个许可还没有被占用，当前线程获取许可并继续执行；如果许可已经被占用，当前线程阻塞，等待获取许可。

LockSupport是可不重入的，如果一个线程连续2次调用LockSupport.park()，那么该线程一定会一直阻塞下去。
``` java
public static void main(String[] args) {
     LockSupport.park();
     System.out.println("block.");
}
// 运行以上代码，可以发现主线程一直处于阻塞状态。因为许可默认是被占用的，调用park()时获取不到许可，所以进入阻塞状态
public static void main(String[] args) {
     Thread thread = Thread.currentThread();
     LockSupport.unpark(thread); // 释放许可
     LockSupport.park(); // 获取许可
     System.out.println("b");
}
// 先释放许可，再获取许可，主线程能够正常终止。
```

LockSupport许可的获取和释放，一般来说是对应的，如果多次unpark，只有一次park也不会出现什么问题，结果是许可处于可用状态。

线程如果因为调用park而阻塞的话，能够响应中断请求（中断状态被设置成true），但是不会抛出InterruptedException 。

**[027]** Condition接口及其实现原理？

Condition接口提供了类似Object的监视器方法，与Lock配合可以实现等待/通知模式。

Condition定义了等待/通知两种类型的方法，当前线程调用这些方法时，需要提前获取到Condition对象关联的锁。

Condition对象是由Lock对象（调用Lock对象的newCondition()方法）创建出来的，Condition是依赖Lock对象的。

``` java
Lock lock = new ReentrantLock();
Condition condition = lock.newCondition();
lock.lock();
try {
    condition.await();
} finally {
    lock.unlock();
}
lock.lock();
try {
    condition.signal();
} finally {
    lock.unlock();
}
```

一般都会将Condition对象作为成员变量。当调用await()方法后，当前线程会释放锁并在此等待，而其他线程调用Condition对象的signal()方法，通知当前线程后，当前线程才从await()方法返回，并且在返回前已经获取了锁。

- void await() throws InterruptedException; // 当前线程进入等待状态直到被通知，其他线程调用该Condition的signal或者signalAll方法，而当前线程被选中唤醒
- void awaitUninterruptibly(); // 当前线程进入等待状态直到被通知，对中断不响应
- long awaitNanos(long nanosTimeout) throws InterruptedException; // 当前线程进入等待状态直到被通知、中断或超时。返回值表示剩余时间，如果在nanosTimeout纳秒之前被唤醒
- boolean await(long time, TimeUnit unit) throws InterruptedException; // 当前线程进入等待状态直到被通知、中断或超时，如果没有到指定时间被通知返回true，否则返回false
- void signal(); // 唤醒一个等待在Condition上的线程，该线程从等待方法返回之前必须获得与Condition相关联的锁

ConditionObject是同步器AbstractQueuedSynchronizer的内部类，因为Condition的操作需要获取相关联的锁，所以作为同步器的内部类也较为合理。

每个Condition对象都包含着一个队列，该队列是Condition对象实现等待/通知功能的关键。主要包括：等待队列、等待和通知

  同步队列和等待队列的概念。
  
  同步队列存放着竞争同步资源的线程的引用（不是存放线程），而等待队列存放着待唤醒的线程的引用。
  
  同步队列中存放着一个个节点，当线程获取同步状态失败时，同步器会将当前线程以及等待状态等信息构造成为一个节点并将其加入同步队列，首节点表示的获取同步状态成功的线程节点。

等待队列
- 等待队列是一个FIFO的队列，在队列中的每个节点都包含了一个线程引用，该线程就是在Condition对象上等待的线程，如果一个线程调用了Condition.await()方法，那么该线程将会释放锁、构造成节点加入等待队列并进入等待状态
- 一个Condition包含一个等待队列，Condition拥有首节点（firstWaiter）和尾节点（lastWaiter）。当前线程调用Condition.await()方法，将会以当前线程构造节点，并将节点从尾部加入等待队列
- 节点引用更新的过程并没有使用CAS保证，原因在于调用await()方法的线程必定是获取了锁的线程，也就是说该过程是由锁来保证线程安全的。
- 在Object的监视器模型上，一个对象拥有一个同步队列和等待队列，而并发包中的Lock（更确切地说是同步器）拥有一个同步队列和多个等待队列，

等待await
- 调用Condition的await()方法（或者以await开头的方法），会使当前线程进入等待队列并释放锁，同时线程状态变为等待状态。
- 当从await()方法返回时，当前线程一定获取了Condition相关联的锁。
- 如果从队列（同步队列和等待队列）的角度看await()方法，当调用await()方法时，相当于同步队列的首节点（获取了锁的节点）移动到Condition的等待队列中
- 调用该方法的线程成功获取了锁的线程，也就是同步队列中的首节点，该方法会将当前线程构造成节点并加入等待队列中，然后释放同步状态，唤醒同步队列中的后继节点，然后当前线程会进入等待状态。
- 当等待队列中的节点被唤醒，则唤醒节点的线程开始尝试获取同步状态。
- 如果不是通过其他线程调用Condition.signal()方法唤醒，而是对等待线程进行中断，则会抛出InterruptedException

通知signal
- 调用Condition的signal()方法，将会唤醒在等待队列中等待时间最长的节点（首节点），在唤醒节点之前，会将节点移到同步队列中 
- 调用该方法的前置条件是当前线程必须获取了锁，可以看到signal()方法进行了isHeldExclusively()检查，也就是当前线程必须是获取了锁的线程。
- 接着获取等待队列的首节点，将其移动到同步队列并使用LockSupport唤醒节点中的线程
    
**[028]** Fork/Join框架的理解？

Fork/Join框架是JDK7提供了的一个用于并行执行任务的框架，就是一个把大任务分割成若干个小任务，最终把每个小任务结果汇总起来得到大任务结果的框架。类似归并排序。

它所使用的线程模式——工作窃取模式

任务分割出的子任务会添加到当前工作线程所维护的双端队列中，进入队列的头部。当一个工作线程的队列里暂时没有任务时，它会随机从其他工作线程的队列的尾部获取一个任务（工作窃取算法）。

ForkJoinTask，使用Fork/Join框架，首先需要创建一个ForkJoin任务。该类提供了在任务中执行fork和join的机制。
通常情况下我们不需要直接集成ForkJoinTask类，只需要继承它的子类，Fork/Join框架提供了两个子类：
- RecursiveAction，用于没有返回结果的任务
- RecursiveTask，用于有返回结果的任务

ForkJoinTask需要通过ForkJoinPool来执行

ForkJoinPool由ForkJoinTask数组和ForkJoinWorkerThread数组组成，ForkJoinTask数组负责将存放程序提交给ForkJoinPool，而ForkJoinWorkerThread负责执行这些任务。

去继承RecursiveAction或者是RecursiveTask，有一个抽象方法compute()，区别在于，前者是没有返回值的，后者是有返回值的，类似Runnable和Callable的关系。

并发编程的目的就是为了压榨CPU资源，提高程序运行速度。

有时候同样完成一个任务，多线程还不如单线程快，比如说在单核的CPU上，多线程的上下文切换也是一笔不小的开销，所以在单核的CPU上多线程还不如单线程速度快。

当任务越大时，Fork/Join框架的优势就越明显

拆分的临界值的设定对Fork/Join框架执行的速度也很关键，太大了没有充分拆分任务压榨CPU，太小了就会拆分过度，浪费太多时间在拆分上
```java
public class CountTask extends RecursiveTask<Integer>{
    private int start;
    private int end;
    public CountTask(int start,int end){
        this.start = start; this.end = end;
    }
    @Override
    protected Integer compute() {
        int sum = 0;
        //如果任务足够小就计算
        boolean canCompute = (end - start) <= 2;
        if(canCompute){
            for(int i=start;i<=end;i++){
                sum += i;
            }
        }else{
            int middle = (start + end) / 2;
            CountTask left = new CountTask(start,middle);
            CountTask right = new CountTask(middle+1,end);
            //执行子任务
            left.fork();
            right.fork();
            //获取子任务结果
            int lResult = left.join();
            int rResult = right.join();
            sum = lResult + rResult;
        }
        return sum;
    }
    
    public static void main(String[] args){
        ForkJoinPool pool = new ForkJoinPool();
        CountTask task = new CountTask(1,4);
        Future<Integer> result = pool.submit(task);
        if(task.isCompletedAbnormally()){
            System.out.println(task.getException());
        }
        try {
            System.out.println(result.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```
当调用ForkJoinTask的fork方法时，程序会把任务放在ForkJoinWorkerThread的pushTask的workQueue中，异步地执行这个任务，然后立即返回结果

pushTask方法把当前任务存放在ForkJoinTask数组队列里。然后再调用ForkJoinPool的signalWork()方法唤醒或创建一个工作线程来执行任务。

**[029]** wait()和sleep()的区别？

这两个方法来自不同的类分别是Thread和Object。

最主要是一个线程对象调用了sleep方法，并不会释放他所持有的所有对象锁，所以也就不会影响其他进程对象的运行。而wait方法释放了锁，使得其他线程可以使用同步控制块或者方法(锁代码块和方法锁)。  

sleep不出让系统资源；wait是进入线程等待池等待，让出系统资源，其他线程可以占用CPU。

从使用范围来看，wait、notify和notifyAll只能在同步控制方法或者同步控制块里面使用，而sleep可以在任何地方使用

sleep必须捕获异常，而wait，notify和notifyAll不需要捕获异常。

sleep方法属于Thread类中方法，表示让一个线程进入睡眠状态，等待一定的时间之后，自动醒来进入到可运行状态，不会马上进入运行状态，因为线程调度机制恢复线程的运行也需要时间，

sleep的过程中过程中有可能被其他对象调用它的interrupt()，产生InterruptedException异常，如果程序不捕获这个异常，线程就会异常终止，进入TERMINATED状态，如果程序捕获了这个异常，那么程序就会继续执行catch语句块(可能还有finally语句块)以及以后的代码。  

Thread.sleep(0)的作用是“触发操作系统立刻重新进行一次CPU竞争”。

注意sleep()方法是一个静态方法，也就是说他只对当前对象有效，通过t.sleep()让t对象进入sleep，这样的做法是错误的，它只会是使当前线程被sleep 而不是t线程  

wait()属于Object的成员方法，一旦一个对象调用了wait方法，必须要采用notify()和notifyAll()方法唤醒该进程;

如果线程拥有某个或某些对象的同步锁，那么在调用了wait()后，这个线程就会释放它持有的所有同步资源，而不限于这个被调用了wait()方法的对象。

wait()方法也同样会在wait的过程中有可能被其他对象调用interrupt()方法而产生异常

**[030]** 线程的五个状态（五种状态，创建、就绪、运行、阻塞和死亡）?

新建状态(New)： 
- 当用new操作符创建一个线程时，new Thread()，线程还没有开始运行，此时线程处在新建状态。当一个线程处于新生状态时，程序还没有开始运行线程中的代码

就绪状态(Runnable)
- 一个新创建的线程并不自动开始运行，要执行线程，必须调用线程的start()方法。当线程对象调用start()方法即启动了线程，start()方法创建线程运行的系统资源，并调度线程运行run()方法。
- 当start()方法返回后，线程就处于就绪状态。
- 处于就绪状态的线程并不一定立即运行run()方法，线程还必须同其他线程竞争CPU时间，只有获得CPU时间才可以运行线程。
- 因为在单CPU的计算机系统中，不可能同时运行多个线程，一个时刻仅有一个线程处于运行状态。
- 因此此时可能有多个线程处于就绪状态。
- 对多个处于就绪状态的线程是由Java运行时系统的线程调度程序(thread scheduler)来调度的。

运行状态(Running)
- 当线程获得CPU时间后，它才进入运行状态，真正开始执行run()方法.

阻塞状态(Blocked)
- 线程运行过程中，可能由于各种原因进入阻塞状态:
  1. 线程通过调用sleep方法进入睡眠状态；
  2. 线程调用一个在I/O上被阻塞的操作，即该操作在输入输出操作完成之前不会返回到它的调用者；
  3. 线程试图得到一个锁，而该锁正被其他线程持有；
  4. 线程在等待某个触发条件；等等

死亡状态(Dead)
- 有两个原因会导致线程死亡：
  1. run方法正常退出而自然死亡，
  2. 一个未捕获的异常终止了run方法而使线程猝死。
- 为了确定线程在当前是否存活着（就是要么是可运行的，要么是被阻塞了），需要使用isAlive方法。
- 如果是可运行或被阻塞，这个方法返回true； 如果线程仍旧是new状态且不是可运行的， 或者线程死亡了，则返回false.

**[031]** start()方法和run()方法的区别？

run()，只是调用了一个普通方法，并没有启动另一个线程，程序还是会按照顺序执行相应的代码。

start()，表示重新开启一个线程，不必等待其他线程运行完，只要得到CPU就可以运行该线程。

**[032]** Runnable接口和Callable接口的区别？

Callable接口和Runnable接口相似，区别就是Callable需要实现call()方法，而Runnable需要实现run()方法。

Callable可以返回一个类型V，而Runnable不可以。

Callable能够抛出checked exception，而Runnable不可以。

Runnable是自从JDK1就有了，而Callable是JDK5之后才加上去的。

Callable和Runnable都可以应用于Executor框架。而Thread类只支持Runnable。

Callable与Executor框架联合在一起，在任务完成时可立刻获得一个更新了的Future。而Runable却要自己处理。

Future接口，一般都是取回Callable执行的状态用的。其中的主要方法：
- cancel()，取消Callable的执行，当Callable还没有完成时
- get()，获得Callable的返回值
- isCanceled()，判断是否取消了
- isDone()，判断是否完成

用Executor框架来构建线程池，应该要做的事：
- 调用Executors类中的静态方法newCachedThreadPool(必要时创建新线程，空闲线程会被保留60秒)或newFixedThreadPool(包含固定数量的线程池)等，返回的是一个实现了ExecutorService接口的ThreadPoolExecutor类或者是一个实现了ScheduledExecutorServiece接口的类对象。
- 调用submit提交Runnable或Callable对象。
- 如果想要取消一个任务，或如果提交Callable对象，那就要保存好返回的Future对象。
- 当不再提交任何任务时，调用shutdown方法。

**[033]** volatile关键字的作用？

Java语言是支持多线程的，为了解决线程并发的问题，在语言内部引入了同步块(synchronized)和volatile关键字机制。

synchronized修饰的方法或者代码块。

同步块，通过synchronized关键字来实现，所有加上synchronized的语句块，在多线程访问的时候，同一时刻只能有一个线程能够用

用volatile修饰的变量，线程在每次使用变量的时候，都会读取变量修改后的最新的值。

每一个线程运行时都有一个线程栈，线程栈保存了线程运行时候变量值信息。

当线程访问某一个对象时候值的时候，首先通过对象的引用找到对应在堆内存的变量的值，然后把堆内存变量的具体值加载到线程本地内存中，建立一个变量副本，之后线程就不再和对象在堆内存变量值有任何关系，而是直接修改副本变量的值，在修改完之后的某一个时刻（线程退出之前），自动把线程变量副本的值回写到对象在堆中变量。这样在堆中的对象的值就产生变化了。

并发编程中三个问题：原子性问题，可见性问题，有序性问题
- Java内存模型只保证了基本读取和赋值是原子性操作。如果要实现更大范围操作的原子性，可以通过synchronized和Lock来实现，任一时刻只有一个线程执行该代码块，从而保证了原子性。。
- Java提供了volatile关键字来保证可见性。通过synchronized和Lock也能够保证可见性，在释放锁之前会将对变量的修改刷新到主存当中，因此可以保证可见性。
- Java里面可以通过volatile关键字来保证一定的“有序性”。可以通过synchronized和Lock来保证有序性，每个时刻是有一个线程执行同步代码，自然就保证了有序性。

Java内存模型具备一些先天的“有序性”，即不需要通过任何手段就能够得到保证的有序性，这个通常也称为`happens-before`原则。

如果两个操作的执行次序无法从`happens-before`原则推导出来，那么它们就不能保证它们的有序性，虚拟机可以随意地对它们进行重排序。

`happens-before`原则（先行发生原则）：
- 程序次序规则：一个线程内，按照代码顺序，书写在前面的操作先行发生于书写在后面的操作
- 锁定规则：一个unLock操作先行发生于后面对同一个锁额lock操作
- volatile变量规则：对一个变量的写操作先行发生于后面对这个变量的读操作
- 传递规则：如果操作A先行发生于操作B，而操作B又先行发生于操作C，则可以得出操作A先行发生于操作C
- 线程启动规则：Thread对象的start()方法先行发生于此线程的每个一个动作
- 线程中断规则：对线程interrupt()方法的调用先行发生于被中断线程的代码检测到中断事件的发生
- 线程终结规则：线程中所有的操作都先行发生于线程的终止检测，我们可以通过Thread.join()方法结束、Thread.isAlive()的返回值手段检测到线程已经终止执行
- 对象终结规则：一个对象的初始化完成先行发生于他的finalize()方法的开始

一个共享变量（类的成员变量、类的静态成员变量）被volatile修饰之后，那么就具备了两层语义：
- 保证了不同线程对这个变量进行操作时的可见性，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的。
- 禁止进行指令重排序。

“观察加入volatile关键字和没有加入volatile关键字时所生成的汇编代码发现，加入volatile关键字时，会多出一个lock前缀指令”

lock前缀指令实际上相当于一个内存屏障（也成内存栅栏），内存屏障会提供3个功能：
- 它确保指令重排序时不会把其后面的指令排到内存屏障之前的位置，也不会把前面的指令排到内存屏障的后面；即在执行到内存屏障这句指令时，在它前面的操作已经全部完成；
- 它会强制将对缓存的修改操作立即写入主存；
- 如果是写操作，它会导致其他CPU中对应的缓存行无效。

synchronized关键字是防止多个线程同时执行一段代码，那么就会很影响程序执行效率，而volatile关键字在某些情况下性能要优于synchronized，但是要注意volatile关键字是无法替代synchronized关键字的，因为volatile关键字无法保证操作的原子性。

使用volatile必须具备以下2个条件：
- 对变量的写操作不依赖于当前值
- 该变量没有包含在具有其他变量的不变式中

``` java
//线程1:
context = loadContext(); // 语句1
volatile inited = true; // 语句2，因为当执行到语句2时，必定能保证context已经初始化完毕
//线程2:
while(!inited ){ sleep() }
doSomethingwithconfig(context);
```
还有双重检查（Double-Check）

**[034]** Java中如何获取到线程dump文件？

如果java程序崩溃生成core文件，jstack工具可以用来获得core文件的java stack和native stack的信息，从而可以轻松地知道java程序是如何崩溃和在程序何处发生问题。
- 使用jps [-l]命令查看本机所有java进程pid
- 使用jstack [-l] pid > xxx.log将所有线程信息输入到指定文件中 ，如果程序无响应：使用 jstack -F [-m] [-l] pid >xxx.log强制打印栈信息 
- 使用top -H -p pid找出占用cpu高（或执行时间长）的线程pid 
- 将占用cpu高的线程pid转换成16进制
- 将转换后的pid在开始输出的dump文件（xxx.log）中搜索对应线程信息 
- 对dump文件分析

**[035]** 线程和进程有什么区别？

线程是CPU独立运行和独立调度的基本单位，进程是资源的分配和调度的一个独立单元。

进程具有独立的空间地址，一个进程崩溃后，在保护模式下不会对其它进程产生影响。

线程只是一个进程的不同执行路径，线程有自己的堆栈和局部变量。

同一个进程中可以包括多个线程，并且线程共享整个进程的资源（寄存器、堆栈、上下文），一个进行至少包括一个线程。

进程的创建调用fork或者vfork，而线程的创建调用pthread_create，进程结束后它拥有的所有线程都将销毁，而线程的结束不会影响同个进程中的其他线程的结束

线程中执行时一般都要进行同步和互斥，因为他们共享同一进程的所有资源

**[036]** 线程实现的方式有几种（四种）？
1. 继承Thread类，重写run方法
2. 实现Runnable接口，重写run方法，实现Runnable接口的实现类的实例对象作为Thread构造函数的target
3. 通过Callable和FutureTask创建线程
4. 通过线程池创建线程

**[037]** 高并发、任务执行时间短的业务怎样使用线程池？并发不高、任务执行时间长的业务怎样使用线程池？并发高、业务执行时间长的业务怎样使用线程池？
1. 高并发、任务执行时间短的业务，线程池线程数可以设置为CPU核数+1，减少线程上下文的切换
2. 并发不高、任务执行时间长的业务要区分开看：
- 假如是业务时间长集中在IO操作上，也就是IO密集型的任务，因为IO操作并不占用CPU，所以不要让所有的CPU闲下来，可以加大线程池中的线程数目，让CPU处理更多的业务
- 假如是业务时间长集中在计算操作上，也就是计算密集型任务，这个就没办法了，和（1）一样吧，线程池中的线程数设置得少一些，减少线程上下文的切换
3. 并发高、业务执行时间长，解决这种类型任务的关键不在于线程池而在于整体架构的设计，看看这些业务里面某些数据是否能做缓存是第一步，增加服务器是第二步，至于线程池的设置，设置参考（2）。最后，业务执行时间长的问题，也可能需要分析一下，看看能不能使用中间件对任务进行拆分和解耦。

合理的配置线程池的大小
- 任务的性质：CPU密集型任务、IO密集型任务、混合型任务。
- 任务的优先级：高、中、低。
- 任务的执行时间：长、中、短。
- 任务的依赖性：是否依赖其他系统资源，如数据库连接等。

最佳线程数目 = （（线程等待时间+线程CPU时间）/线程CPU时间 ）* CPU数目

比如平均每个线程CPU运行时间为0.5s，而线程等待时间（非CPU运行时间，比如IO）为1.5s，CPU核心数为8，那么根据上面这个公式估算得到：((0.5+1.5)/0.5)*8=32。

**[038]** 如果你提交任务时，线程池队列已满，这时会发生什么？

ThreadPoolExecutors中的submit()方法会抛出一个RejectedExecutionException异常

**[039]** 锁的等级：方法锁、对象锁、类锁?

**[040]** 如果同步块内的线程抛出异常会发生什么？

synchronized会释放锁，lock需要在finally里面释放

lock同步是在代码层进行资源的抢占控制，而用synchronization进行的同步JVM层的控制

被synchronizatioin修饰的语句块，其实在编译成字节码时，会被monitorenter和monitorexit指令包围，多个线程在进入monitorenter控制的字节码时，需要进入等候队列排队，同时会隐式的在字节码里添加异常处理器，在异常处理器里添加monitorexit指令，保证了同步块里出现异常时能释放资源。

**[041]** 并发编程（concurrency）并行编程（parallellism）有什么区别？

并发（concurrency）和并行（parallellism）是：
- 解释一：并行是指两个或者多个事件在同一时刻发生；而并发是指两个或多个事件在同一时间间隔发生。
- 解释二：并行是在不同实体上的多个事件，并发是在同一实体上的多个事件。
- 解释三：在一台处理器上“同时”处理多个任务，在多台处理器上同时处理多个任务。如hadoop分布式集群

所以并发编程的目标是充分的利用处理器的每一个核，以达到最高的处理性能。

并发的关键是你有处理多个任务的能力，不一定要同时。并行的关键是你有同时处理多个任务的能力。

**[042]** 如何保证多线程下`i++`结果正确？

锁和原子类

**[043]** 一个线程如果出现了运行时异常会怎么样?

如果该异常被捕获或抛出，则程序继续运行。

如果异常没有被捕获该线程将会停止执行。 

**[044]** 如何在两个线程之间共享数据?

只需创建一个Runnable,这个Runnable里有那个共享数据。

将共享数据封装成另外一个对象中封装成另外一个对象中，然后将这个对象逐一传递给各个Runnable对象，每个线程对共享数据的操作方法也分配到那个对象身上完成，这样容易实现针对数据进行各个操作的互斥和通信

将Runnable对象作为偶一个类的内部类，共享数据作为这个类的成员变量，每个线程对共享数据的操作方法也封装在外部类，以便实现对数据的各个操作的同步和互斥，作为内部类的各个Runnable对象调用外部类的这些方法。

**[045]** 生产者消费者模型的作用是什么?

产生数据的模块，就形象地称为生产者；而处理数据的模块，就称为消费者；生产者和消费者之间的中介就叫做缓冲区。

解耦，即降低生产者和消费者之间的依赖关系。 

支持并发，即生产者和消费者可以是两个独立的并发主体，互不干扰的运行。 

支持忙闲不均，如果制造数据的速度时快时慢，缓冲区可以对其进行适当缓冲。

**[046]** 怎么唤醒一个阻塞的线程？

sleep()方法、suspend()和resume()方法、yield()方法、wait()和notify()方法、LockSupport提供的park()和unpark()方法

**[047]** Java中用到的线程调度算法是什么？

在Java多线程环境中，为保证所有线程的执行能按照一定的规则执行，JVM实现了一个线程调度器，它定义了线程调度的策略，对于CPU运算的分配都进行了规定，按照这些特定的机制为多个线程分配CPU的使用权。

一般线程调度模式分为两种——抢占式调度和协同式调度。

抢占式调度指的是每条线程执行的时间、线程的切换都由系统控制，系统控制指的是在系统某种运行机制下，可能每条线程都分同样的执行时间片，也可能是某些线程执行的时间片较长，甚至某些线程得不到执行的时间片。

在这种机制下，一个线程的堵塞不会导致整个进程堵塞。

协同式调度指某一线程执行完后主动通知系统切换到另一线程上执行，这种模式就像接力赛一样，一个人跑完自己的路程就把接力棒交接给下一个人，下个人继续往下跑。线程的执行时间由线程本身控制，线程切换可以预知，不存在多线程同步问题，

但它有一个致命弱点：如果一个线程编写有问题，运行到一半就一直堵塞，那么可能导致整个系统崩溃。

Java使用的线程调度是抢占式调度，在JVM中体现为让可运行池中优先级高的线程拥有CPU使用权，如果可运行池中线程优先级一样则随机选择线程，

但要注意的是实际上一个绝对时间点只有一个线程在运行（这里是相对于一个CPU来说，如果你的机器是多核的还是可能多个线程同时运行的），直到此线程进入非可运行状态或另一个具有更高优先级的线程进入可运行线程池，才会使之让出CPU的使用权，更高优先级的线程抢占了优先级低的线程的CPU。

**[048]** 单例模式的线程安全性?

**[049]** 线程类的构造方法、静态块是被哪个线程调用的?

线程类的构造方法、静态块是被new这个线程类所在的线程所调用的，而run方法里面的代码才是被线程自身所调用的。

**[050]** 同步方法和同步块，哪个是更好的选择?

一般同步的范围越大，性能就越差，一般需要加锁进行同步的时候，肯定是范围越小越好，这样性能更好

**[051]** 如何检测死锁？怎么预防死锁？

死锁是指多个线程因竞争资源而造成的一种僵局（互相等待），若无外力作用，这些进程都将无法向前推进。

系统资源的竞争、进程推进顺序非法

死锁避免的基本思想：
- 系统对进程发出的每一个系统能够满足的资源申请进行动态检查，并根据检查结果决定是否分配资源，如果分配后系统可能发生死锁，则不予分配，否则予以分配，这是一种保证系统不进入死锁状态的动态策略。

两种死锁避免算法：
- 进程启动拒绝：如果一个进程的请求会导致死锁，则不启动该进程。
- 资源分配拒绝：如果一个进程增加的资源请求会导致死锁，则不允许此分配(银行家算法)。

死锁检测算法：
- 每个进程、每个资源制定唯一编号,设定一张资源分配表，记录各进程与占用资源之间的关系，出现环路引起死锁

两种常用的死锁解除方法：
- 资源剥夺法。挂起某些死锁进程，并抢占它的资源，将这些资源分配给其他的死锁进程。但应防止被挂起的进程长时间得不到资源，而处于资源匮乏的状态。
- 撤销进程法。强制撤销部分、甚至全部死锁进程并剥夺这些进程的资源。撤销的原则可以按进程优先级和撤销进程代价的高低进行。


**[052]** 其他
Jconsole是JDK自带的图形化界面工具，使用JDK给我们的的工具JConsole，可以通过打开cmd然后输入jconsole打开。

Jstack是JDK自带的命令行工具，主要用于线程Dump分析。





