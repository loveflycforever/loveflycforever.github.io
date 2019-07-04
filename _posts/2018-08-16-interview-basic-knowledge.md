---
layout: post
title:  "Java 基础知识"
subtitle: "为了面试 Part 1：Java 基础知识"
tags: [面试]
comments: true
---
**[001]** 八种基本数据类型的大小，以及它们的封装类

int(4字节)、short(2字节)、float(4字节)、double(8字节)、long(8字节)、byte(1字节)、boolean(4字节或者1字节)、char(2字节)

Number{Integer、Short、Float、Double、Long、Byte}、Boolean、Character。

**[002]** 引用数据类型

类、接口类型、数组类型、枚举类型、注解类型。

基本数据类型和引用类型的区别主要在于基本数据类型是分配在栈上的，而引用类型是分配在堆上的（需要java中的栈、堆概念）。

**[003]** switch-case语句能否用String做参数

1.7之后可以，会对字符串进行hashcode。

**[004]** equals与==的区别

"=="比较的是变量(栈)内存中存放的对象的(堆)内存地址，用来判断两个对象的地址是否相同，即是否是指相同一个对象。比较的是真正意义上的指针操作。

equals用来比较的是两个对象的内容是否相等，由于所有的类都是继承自java.lang.Object类的，所以适用于所有对象，如果没有对该方法进行覆盖的话，调用的仍然是Object类中的方法，而Object中的equals方法返回的却是==的判断。

"=="比"equals"运行速度快,因为"=="只是比较引用。

**[005]** 自动装箱，常量池
运算==、+、-、*、/时，会将封装类进行拆箱，对基础数据类型进行运算
装箱操作会创建对象，频繁的装箱操作会消耗许多内存，影响性能，所以可以避免装箱的时候应该尽量避免。

方法区里存放着类的版本，字段，方法，接口和常量池等。
方法区里的class文件信息包括：
magic 魔数[u4，1]，
minor_version 副版本号[u2，1]，
major_version 主版本号[u2，1]，
constant_pool_count 常量池计数器[u2，1]，
constant_pool 常量池[cp_info，constant_pool_count - 1]，
access_flags 访问标志[u2，1]，
this_class 类索引[u2，1]，
super_class 父类索引[u2，1]，
interfaces_count 接口计数器[u2，1]，
interfaces 接口[u2，interfaces_count]，
fields_count 字段计数器[u2，1]，
fields 字段[field_info，fields_count]，
methods_count 方法计数器[u2，1]，
methods 方法[method_info，methods_count]，
attribute_count 属性计数器[u2，1]，
attributes 属性[attribute_info，attributes_count]
常量池里存储着用于存放编译期生成的字面量和符号引用。这部分内容将在类加载后进入方法区的运行时常量池中存放（String.intern()）。
字面量包括文本字符串、被声明为final的常量值、基本数据类型的值、其他。
符号引用包括类和结构的完全限定名、字段名称和描述符、方法名称和描述符。
在JDK6.0及之前版本，字符串常量池是放在Perm Gen区(也就是方法区)中；在JDK7.0版本，字符串常量池被移到了堆中了。

6. Object有哪些公用方法
Object是所有类的父类，任何类都默认继承Object。
clone()
保护方法，实现对象的浅复制，只有实现了Cloneable接口才可以调用该方法，否则抛出CloneNotSupportedException异常
equals()
在Object中与==是一样的，子类一般需要重写该方法
hashCode()
该方法用于哈希查找，重写了equals方法一般都要重写hashCode方法。这个方法在一些具有哈希功能的Collection中用到
getClass()
final方法，获得运行时类型
wait()
使当前线程等待该对象的锁，当前线程必须是该对象的拥有者，也就是具有该对象的锁。wait()方法一直等待，直到获得锁或者被中断。wait(long timeout)设定一个超时间隔，如果在规定时间内没有获得锁就返回。
调用该方法后当前线程进入睡眠状态，直到以下事件发生：
1. 其他线程调用了该对象的notify方法
2. 其他线程调用了该对象的notifyAll方法
3. 其他线程调用了interrupt中断该线程
4. 时间间隔到了
此时该线程就可以被调度了，如果是被中断的话就抛出一个InterruptedException异常
notify()
唤醒在该对象上等待的某个线程
notifyAll()
唤醒在该对象上等待的所有线程
toString()
转换成字符串，一般子类都有重写，否则打印句柄
finalize方法()
该方法用于释放资源。因为无法确定该方法什么时候被调用，很少使用。

7. Java的四种引用，强弱软虚，用到的场景
强引用（StrongReference）
A a = new A()
有引用变量指向时永远不会被垃圾回收。
软引用（SoftReference）
SoftReference<T> ref = new SoftReference<T>(new T());
或者
ReferenceQueue queue = new ReferenceQueue();
SoftReference ref = new SoftReference(aMyObject, queue); 
内存充足JVM不会回收，内存不充足时JVM会回收。
清除ref
SoftReference ref = null;
while ((ref = (EmployeeRef) q.poll()) != null) {
// 清除ref
}
弱引用（WeakReference）
WeakReference<T> ref = new WeakReference<T>(new T());  
当JVM进行垃圾回收时，无论内存是否充足，都会回收。
虚引用（PhantomReference）
ReferenceQueue<T> queue = new ReferenceQueue<T>();  
PhantomReference<T> ref = new PhantomReference<T>(new T(), queue);  
在使用软引用和弱引用的时候，我们可以显示地通过System.gc()来通知JVM进行垃圾回收，但是要注意的是，虽然发出了通知，JVM不一定会立刻执行，也就是说这句是无法确保此时JVM一定会进行垃圾回收的。

8. Hashcode的作用
9. HashMap的hashcode的作用
10. 为什么重载hashCode方法？
hashCode方法的主要作用是为了配合基于散列的集合一起正常运行。
put方法是用来向HashMap中添加新的元素，从put方法的具体实现可知，会先调用hashCode方法得到该元素的hashCode 值，然后查看table中是否存在该hashCode值，如果存在则调用equals方法重新确定是否存在该元素，如果存在，则更新value值，否则将 新的元素添加到HashMap中。
在重写equals方法的同时，必须重写hashCode方法。

11. ArrayList、LinkedList、Vector的区别
用Iterator实现单向遍历，也可用ListIterator实现双向遍历
Arraylist和Vector是采用数组方式存储数据，此数组元素数大于实际存储的数据以便增加插入元素，都允许直接序号索引元素，但是插入数据要涉及到数组元素移动等内存操作，所以插入数据慢，查找有下标，所以查询数据快，
Vector由于使用了synchronized方法-线程安全，所以性能上比ArrayList要差，
LinkedList使用双向链表实现存储，按序号索引数据需要进行向前或向后遍历，但是插入数据时只需要记录本项前后项即可，插入数据较快。

12. String、StringBuffer与StringBuilder的区别
运行速度快慢为：StringBuilder > StringBuffer > String
String为字符串常量，而StringBuilder和StringBuffer均为字符串变量，即String对象一旦创建之后该对象是不可更改的，但后两者的对象是变量，是可以更改的。
String：适用于少量的字符串操作的情况
StringBuilder与StringBuffer有公共父类AbstractStringBuilder
StringBuilder：适用于单线程下在字符缓冲区进行大量操作的情况
StringBuffer：适用多线程下在字符缓冲区进行大量操作的情况

13. Map、Set、List、Queue、Stack的特点与用法
Collection
├List
│├LinkedList
│├ArrayList
│└Vector
│　└Stack
└Set
  ├ HashSet
  │ 　 └ LinkedHashSet
  └ TreeSet
  
Map
├Hashtable
├HashMap
│├ LinkedHashMap
│└ WeakHashMap
├ TreeMap
└ IdentifyHashMap

14. HashMap和HashTable的区别
15. JDK7与JDK8中HashMap的实现
JDK7
JDK7中HashMap采用的是位桶+链表的方式
HashMap底层维护一个数组，数组中的每一项都是一个Entry
当向 HashMap 中 put 一对键值时，它会根据 key的 hashCode 值计算出一个位置， 该位置就是此对象准备往数组中存放的位置。
如果该位置没有对象存在，就将此对象直接放进数组当中；如果该位置已经有对象存在了，则顺着此存在的对象的链开始寻找(为了判断是否是否值相同，map不允许<key,value>键值对重复)， 如果此链上有对象的话，再去使用 equals方法进行比较，如果对此链上的每个对象的 equals 方法比较都为 false，则将该对象放到数组当中，然后将数组中该位置以前存在的那个对象链接到此对象的后面。
当key为null时，都放到table[0]中
JDK8
JDK8中采用的是位桶+链表/红黑树的方式
JDK中Entry的名字变成了Node，原因是和红黑树的实现TreeNode相关联。
当冲突节点数不小于8-1时，转换成红黑树。
JDK8中，当同一个hash值的节点数不小于8时，将不再以单链表的形式存储了，会被调整成一颗红黑树。是JDK7与JDK8中HashMap实现的最大区别。

16. HashMap和ConcurrentHashMap的区别，HashMap的底层源码
如果机器内存足够，并且想要提高查询速度的话可以将加载因子设置小一点；相反如果机器内存紧张，并且对查询速度没有什么要求的话可以将加载因子设置大一点。不过一般我们都不用去设置它，让它取默认值0.75就好了。

ConcurrentHashMap是线程安全的。
ConcurrentHashMap采用锁分段技术，将整个Hash桶进行了分段segment，也就是将这个大的数组分成了几个小的片段segment，而且每个小的片段segment上面都有锁存在，那么在插入元素的时候就需要先找到应该插入到哪一个片段segment，然后再在这个片段上面进行插入，而且这里还需要获取segment锁。
ConcurrentHashMap让锁的粒度更精细一些，并发性能更好。

17. ConcurrentHashMap能完全替代HashTable吗
HashTable是一个线程安全的类，它使用synchronized来锁住整张Hash表来实现线程安全，即每次锁住整张表让线程独占。
ConcurrentHashMap允许多个修改操作并发进行，其关键在于使用了锁分离技术。它使用了多个锁来控制对hash表的不同部分进行的修改。ConcurrentHashMap内部使用段(Segment)来表示这些不同的部分，每个段其实就是一个小的Hashtable，它们有自己的锁。只要多个修改操作发生在不同的段上，它们就可以并发进行。
HashTable虽然性能上不如ConcurrentHashMap，但并不能完全被取代，两者的迭代器的一致性不同的，HashTable的迭代器是强一致性的，而ConcurrentHashMap是弱一致的。 ConcurrentHashMap的get，clear，iterator 都是弱一致性的。 

Segment下面包含很多个HashEntry列表数组。对于一个key，需要经过三次hash操作，才能最终定位这个元素的位置，这三次hash分别为：
- 对于一个key，先进行一次hash操作，得到hash值h1，也即h1 = hash1(key)；
- 将得到的h1的高几位进行第二次hash，得到hash值h2，也即h2 = hash2(h1高几位)，通过h2能够确定该元素的放在哪个Segment；
- 将得到的h1进行第三次hash，得到hash值h3，也即h3 = hash3(h1)，通过h3能够确定该元素放置在哪个HashEntry。
不lock所有的Segment，遍历所有Segment，累加各个Segment的大小得到整个Map的大小，如果某相邻的两次计算获取的所有Segment的更新的次数（每个Segment都有一个modCount变量，这个变量在Segment中的Entry被修改时会加一，通过这个值可以得到每个Segment的更新操作的次数）是一样的，说明计算过程中没有更新操作，则直接返回这个值。
如果这三次不加锁的计算过程中Map的更新次数有变化，则之后的计算先对所有的Segment加锁，再遍历所有Segment计算Map大小，最后再解锁所有Segment。
ConcurrentHashMap中的key和value值都不能为null，HashMap中key可以为null，HashTable中key不能为null。
ConcurrentHashMap是线程安全的类并不能保证使用了ConcurrentHashMap的操作都是线程安全的！
ConcurrentHashMap的get操作不需要加锁，put操作需要加锁
因为get操作几乎所有时候都是一个无锁操作（get中有一个readValueUnderLock调用，不过这句执行到的几率极小），使得同一个Segment实例上的put和get可以同时进行，这就是get操作是弱一致的根本原因。
ConcurrentHashMap的弱一致性主要是为了提升效率，是一致性与效率之间的一种权衡。要成为强一致性，就得到处使用锁，甚至是全局锁，这就与Hashtable和同步的HashMap一样了。

18. 为什么HashMap是线程不安全的
没有锁，无法保持原子性，存取操作无法保证正确结果。
在多线程存储rehash会陷入死循环。

19. 如何线程安全的使用HashMap
HashMap不是线程安全的，如果想要线程安全的HashMap，可以通过Collections类的静态方法synchronizedMap获得线程安全的HashMap。
Map map = Collections.synchronizedMap(new HashMap());

20. 多并发情况下HashMap是否还会产生死循环
JDK8中使用了红黑树node复制节点时将其放在原节点之后，不像之前的链表放在之前，所以不会发生死锁

21. TreeMap、HashMap、LindedHashMap的区别
HashMap里面存入的键值对在取出的时候是随机的,它根据键的HashCode值存储数据,根据键可以直接获取它的值，具有很快的访问速度。
LinkedHashMap保存了记录的插入顺序（迭代器顺序），在用Iterator遍历LinkedHashMap时，先得到的记录肯定是先插入的.也可以在构造时用带参数，按照应用次数排序。
LinkedHashMap遍历的时候会比HashMap慢，LinkedHashMap的遍历速度只和实际数据有关，和容量无关，而HashMap的遍历速度和他的容量有关。当HashMap容量很大，实际数据较少时，遍历起来可能会比LinkedHashMap慢。
LinkedHashMap 是HashMap的一个子类，如果需要输出的顺序和输入的相同,那么用LinkedHashMap可以实现,它还可以按读取顺序来排列，像连接池中可以应用。
removeEldestEntry(Map.Entry) ，用户可以重写该方法来制定添加元素时的策略。方法内部返回true时，当要往该map中存放（put\putAll）元素时，就先删除最老的对象，然后再添加新对象。
Map<String, Object> map = new LinkedHashMap<String, Object>() {  
    protected boolean removeEldestEntry(Map.Entry<String, Object> eldest) {  
            return size() > 2;  
    }  
}; 
TreeMap实现SortedMap接口，能够把它保存的记录根据键排序（红黑树顺序，可以自定义），默认是按键值的升序排序，也可以指定排序的比较器，当用Iterator 遍历TreeMap时，得到的记录是排过序的。如果要按自然顺序或自定义顺序遍历键，那么TreeMap会更好。
Map<String,String> treeMap = new TreeMap<String,String>(new Comparator<String>(){  
    public int compare(String o1, String o2) {  
        return -o1.compareTo(o2);    
    }  
});  

22. Collection包结构，与Collections的区别
Collection 单列集合
├List
│├LinkedList 线程不安全，增删速度快。 底层数据结构是列表结构。
│├ArrayList 线程不安全，查询速度快。 底层数据结构是数组结构。
│└Vector 线程安全，但速度慢，已被ArrayList替代。底层数据结构是数组结构。
│　└Stack
└Set
  ├ HashSet 线程不安全，存取速度快。依赖元素的hashCode方法和euqals方法保证元素唯一性。
  │ 　 └ LinkedHashSet
  └ TreeSet 线程不安全，可以对Set集合中的元素进行排序。通过compareTo或者compare方法中的来保证元素的唯一性。元素是以二叉树的形式存放的。
  
Map 双列集合
├Hashtable 线程安全，速度快。底层是哈希表数据结构。是同步的。不允许null作为键或者值。
│├ Properties 用于配置文件的定义和操作，使用频率非常高，同时键和值都是字符串。是集合中可以和IO技术相结合的对象。
├HashMap 线程不安全，速度慢。底层也是哈希表数据结构。是不同步的。允许null作为键或者值。替代了Hashtable.
│├ LinkedHashMap 可以保证HashMap集合有序。存入的顺序和取出的顺序一致。
│└ WeakHashMap
├ TreeMap 可以用来对Map集合中的键进行排序。
└ IdentifyHashMap

Collection是集合类的上级接口，子接口主要有Set 和List、Map。
boolean add(E e)
boolean addAll(Collection<? extendsE> c)
void clear()
boolean contains(Object o)
boolean equals(Object o)
int hashCode()
boolean isEmpty()
Iterator<E> iterator()
boolean remove(Object o)
boolean removeAll(Collection<?>c)
boolean retainAll(Collection<?>c)
int size()
Object[] toArray()
<T>T[] toArray(T[] a)
Collections是针对集合类的一个帮助类，提供了操作集合的工具方法：一系列静态方法实现对各种集合的搜索、排序、线程安全化等操作。 

23. try-catch-finally，try里有return，finally还执行么
在finally里面是不能执行return语句的，如果在finally中使用了return，则会提示这样的错误：“控制不能离开 finally子句主体”。

24. Excption与Error包结构，OOM你遇到过哪些情况，SOF你遇到过哪些情况
Throwable
├Exception
│└RuntimeException
└Error
编译器不会检查运行时异常
按照Java惯例，是不应该是实现任何新的Error子类的！
OOM 内存溢出 OutOfMemory
SOF 堆栈溢出 StackOverflow

25. Java(OOP)面向对象的三个特征与含义
封装、继承、多态

26. Override和Overload的含义与区别
方法的重写(Overriding)和重载(Overloading)是Java多态性的不同表现。   
重写(Overriding)是父类与子类之间多态性的一种表现，而重载(Overloading)是一个类中多态性的一种表现。

27. Interface与abstract类的区别
abstract class和interface是支持抽象类定义的两种机制。
  抽象类可以有构造方法，接口中不能有构造方法。
  抽象类中可以有普通成员变量，接口中没有普通成员变量
  抽象类中可以包含非抽象的普通方法，接口中的所有方法必须都是抽象的，不能有非抽象的普通方法。
  接口中的抽象方法只能是public类型的。
  抽象类中可以包含静态方法，接口中不能包含静态方法
  抽象类和接口中都可以包含静态成员变量，抽象类中的静态成员变量的访问类型可以任意，但接口中定义的变量只能是public static final类型，并且默认即为public static final类型。
  一个类可以实现多个接口，但只能继承一个抽象类。

28. Static class 与non static class的区别
static class
  用static修饰的是内部类，此时这个内部类变为静态内部类；对测试有用；
  内部静态类不需要有指向外部类的引用；
  静态类只能访问外部类的静态成员，不能访问外部类的非静态成员；
non static class
  非静态内部类需要持有对外部类的引用；
  非静态内部类能够访问外部类的静态和非静态成员；
  一个非静态内部类不能脱离外部类实体被创建；
  一个非静态内部类可以访问外部类的数据和方法；

29. java多态的实现原理
多态是面向对象编程语言的重要特性，它允许基类的指针或引用指向派生类的对象，而在具体访问时实现方法的动态绑定。
Java 对于方法调用动态绑定的实现主要依赖于方法表，但通过类引用调用(invokevitual)和接口引用调用(invokeinterface)的实现则有所不同。  
当某个方法被调用时，JVM 首先要查找相应的常量池，得到方法的符号引用，并查找调用类的方法表以确定该方法的直接引用，最后才真正调用该方法。
Java 的方法调用有两类，动态方法调用与静态方法调用。
静态方法调用是指对于类的静态方法的调用方式，是静态绑定的；
动态方法调用需要有方法调用所作用的对象，是动态绑定的。
类调用 (invokestatic) 是在编译时刻就已经确定好具体调用方法的情况，
实例调用 (invokevirtual) 则是在调用的时候才确定具体的调用方法，这就是动态绑定，也是多态要解决的核心问题。
JVM 的方法调用指令有四个，分别是 invokestatic，invokespecial，invokesvirtual 和 invokeinterface。前两个是静态绑定，后两个是动态绑定的。

30. foreach与正常for循环效率对比
需要循环数组结构的数据时，建议使用普通for循环，因为for循环采用下标访问，对于数组结构的数据来说，采用下标访问比较好。
需要循环链表结构的数据时，一定不要使用普通for循环，这种做法很糟糕，数据量大的时候有可能会导致系统崩溃。
原因：foreach使用的是迭代器
结论：可以下标访问时，使用for，不能下标访问，需要指针访问时，使用foreach。
  
31. Java IO与NIO
java.NIO包里包括三个基本的组件
├buffer：因为NIO是基于缓冲的，所以buffer是最底层的必要类，这也是IO和NIO的根本不同，虽然stream等有buffer开头的扩展类，但只是流的包装类，还是从流读到缓冲区，而NIO却是直接读到buffer中进行操作。因为读取的都是字节，所以在操作文字时，要用charset类进行编解码操作。
├channel：类似于IO的stream，但是不同的是除了FileChannel，其他的channel都能以非阻塞状态运行。FileChannel执行的是文件的操作，可以直接DMA操作内存而不依赖于CPU。其他比如socketchannel就可以在数据准备好时才进行调用。
└selector：用于分发请求到不同的channel，这样才能确保channel不处于阻塞状态就可以收发消息。
Java NIO和IO之间的区别是，IO是面向流的，NIO是面向缓冲区的。
  Java IO面向流意味着每次从流中读一个或多个字节，直至读取所有字节，它们没有被缓存在任何地方。此外，它不能前后移动流中的数据。如果需要前后移动从流中读取的数据，需要先将它缓存到一个缓冲区。
  Java NIO的缓冲导向方法略有不同。数据读取到一个它稍后处理的缓冲区，需要时可在缓冲区中前后移动。这就增加了处理过程中的灵活性。但是，还需要检查是否该缓冲区中包含所有您需要处理的数据。而且，需确保当更多的数据读入缓冲区时，不要覆盖缓冲区里尚未处理的数据。
Java NIO和IO之间的区别是，IO是阻塞IO，NIO是非阻塞IO。
  Java IO的各种流是阻塞的，当一个线程调用read() 或 write()时，该线程被阻塞，直到有一些数据被读取，或数据完全写入。该线程在此期间不能再干任何事情了。
  Java NIO的非阻塞模式，使一个线程从某通道发送请求读取数据，但是它仅能得到目前可用的数据，如果目前没有数据可用时，就什么都不会获取。而不是保持线程阻塞，所以直至数据变的可以读取之前，该线程可以继续做其他的事情。 非阻塞写也是如此。一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情。 线程通常将非阻塞IO的空闲时间用于在其它通道上执行IO操作，所以一个单独的线程现在可以管理多个输入和输出通道（channel）。
Java NIO和IO之间的区别是，NIO有选择器的概念。
  Java NIO的选择器允许一个单独的线程来监视多个输入通道，你可以注册多个通道使用一个选择器，然后使用一个单独的线程来“选择”通道：这些通道里已经有可以处理的输入，或者选择已准备写入的通道。这种选择机制，使得一个单独的线程很容易来管理多个通道。
NIO的buffer可以使用直接内存缓冲区，该缓冲区不在JVM中，性能会比JVM的缓冲区略好，不过会增加相应的垃圾回收的负担，因为JVM缓冲区的性能已经足够好，所以除非在对缓冲有特别要求的地方使用直接缓冲区，尽量使用JVM缓冲。
在老的IO包中，serverSocket和socket都是阻塞式的，因此一旦有大规模的并发行为，而每一个访问都会开启一个新线程。这时会有大规模的线程上下文切换操作（因为都在等待，所以资源全都被已有的线程吃掉了），这时无论是等待的线程还是正在处理的线程，响应率都会下降，并且会影响新的线程。
NIO包中的serverSocket和socket就不是这样，只要注册到一个selector中，当有数据放入通道的时候，selector就会得知哪些channel就绪，这时就可以做响应的处理，这样服务端只有一个线程就可以处理大部分情况（当然有些持续性操作，比如上传下载一个大文件，用NIO的方式不会比IO好）。
Open ServerSocket -> Accept Connection -> Read Request -> Send Response -> Close Connection  ┐
                                                ^───────────────────────────────────────────────┘
首先创建ServerSocket server=new ServerSocket（10000）；
然后接受新的连接请求Socket newConnection=server.accept（）；
对于accept方法的调用将造成阻塞，直到ServerSocket接受到一个连接请求为止。一旦连接请求被接受，服务器可以读客户socket中的请求。 
读取过程中，首先BufferedReader类的readLine（）方法在其缓冲区未满时会造成线程阻塞，只有一定数据填满了缓冲区或者客户关闭了套接字，方法才会返回。其次，它回产生大量的垃圾，BufferedReader创建了缓冲区来从客户套接字读入数据，但是同样创建了一些字符串存储这些数据。虽然BufferedReader内部提供了StringBuffer处理这一问题，但是所有的String很快变成了垃圾需要回收。
同样的问题在发送响应代码中也存在，类似的，读写操作被阻塞而且向流中一次写入一个字符会造成效率低下，所以应该使用缓冲区，但是一旦使用缓冲，流又会产生更多的垃圾。
传统的解决方法，通常在Java中处理阻塞I/O要用到线程（大量的线程）。一般是实现一个线程池用来处理请求
Master Thread
Open ServerSocket -> Accept Connection -> Dispatch to waiting Thread ┐
                                                ^─────────────────┿──────────┘
Worker Thread  ┍────────────────────────────┘
Wait for Dispatch -> Read Request -> Send Response -> Close Connection  ┐
           ^───────────────────────────────────────────────┘
线程使得服务器可以处理多个连接，但是它们也同样引发了许多问题。每个线程拥有自己的栈空间并且占用一些CPU时间，耗费很大，而且很多时间是浪费在阻塞的I/O操作上，没有有效的利用CPU。

32. java反射的作用于原理
在JAVA中，只要给定类的名字，就可以通过反射机制来获取类的所有信息，可以动态的创建对象和编译。
java的类加载器分为BootStrap Class Loader(引导类加载器)，Extensions Class Loader (扩展类加载器),App ClassLoader(或System Class Loader)，当然少不了Custom ClassLoader(用户自定义类加载器)。
其加载过程中会先检查类是否被已加载，检查顺序是自底向上，从Custom ClassLoader到BootStrap ClassLoader逐层检查，只要某个classloader已加载就视为已加载此类，保证此类只所有ClassLoader加载一次。而加载的顺序是自顶向下，也就是由上层来逐层尝试加载此类。
Java中是用Class.forName(classname)来反射类
先检查自己是否已经加载过该类，如果加载过，则直接返回该类，若没有则调用父类的loadClass方法，如果父类中没有，则执行findClass方法去尝试加载此类，也就是我们通常所理解的片面的"反射"了。这个过程主要通过ClassLoader.defineClass方法来完成。defineClass 方法将一个字节数组转换为 Class 类的实例（任何类的对象都是Class类的对象）。
这种新定义的类的实例需要使用 Class.newInstance 来创建，而不能使用new来实例化。
其实说的简单通俗一点，就是在运行期间，如果我们要产生某个类的对象，Java虚拟机（JVM）会检查该类型的Class对象是否已被加载。如果没有被加载，JVM会根据类的名称找到.class文件并加载它。一旦某个类型的Class对象已被加载到内存，就可以用它来产生该类型的所有对象。
Class<?> demo = Class.forName("Reflect.Person");
Method method=demo.getMethod("sayChina");
method.invoke(demo.newInstance());
Person per = (Person) demo.newInstance();
Constructor<?>cons[]=demo.getConstructors();
Person per1= (Person) cons[3].newInstance("Rollen",20);
动态代理
class MyInvocationHandler implements InvocationHandler {
    private Object obj = null;

    public Object bind(Object obj) {
        this.obj = obj;
       return Proxy.newProxyInstance(obj.getClass().getClassLoader(), obj
                .getClass().getInterfaces(), this);
    }

   @Override
    public Object invoke(Object proxy, Method method, Object[] args)
           throws Throwable {
       Object temp = method.invoke(this.obj, args);
        return temp;
     }
 }

MyInvocationHandler demo = new MyInvocationHandler();
Subject sub = (Subject) demo.bind(new RealSubject());
String info = sub.say("Rollen", 20);
 System.out.println(info);
在程序执行中JVM通过装载，链接，初始化这3个步骤完成。
装载是通过类加载器完成的，加载器将.class文件的二进制文件装入JVM的方法区，并且在堆区创建描述这个类的java.lang.Class对象。用来封装数据。 但是同一个类只会被类装载器装载一次
链接就是把二进制数据组装为可以运行的状态。
链接分为校验，准备，解析这3个阶段：
  校验一般用来确认此二进制文件是否适合当前的JVM（版本），
  准备就是为静态成员分配内存空间，。并设置默认值
  解析指的是转换常量池中的代码作为直接引用的过程，直到所有的符号引用都可以被运行程序使用（建立完整的对应关系）。
完成之后，类型也就完成了初始化，初始化之后类的对象就可以正常使用了，直到一个对象不再使用之后，将被垃圾回收。释放空间。当没有任何引用指向Class对象时就会被卸载，结束类的生命周期。
Spring中的IoC的实现原理就是工厂模式加反射机制。
class Factory{
    public static fruit getInstance(String ClassName){
        fruit f=null;
       try{
           f=(fruit)Class.forName(ClassName).newInstance();
      }catch (Exception e) {
           e.printStackTrace();
       }
     return f;
     }
 }
 fruit f=Factory.getInstance("Reflect.Apple");

33. 泛型常用特点
Java中的泛型基本上都是在编译器这个层次来实现的，在生成的Java字节代码中是不包含泛型中的类型信息的。
使用泛型的时候加上的类型参数，会被编译器在编译的时候去掉，这个过程就称为类型擦除。
泛型类并没有自己独有的Class类对象。比如并不存在List<String>.class或是List<Integer>.class，而只有List.class。
静态变量是被泛型类的所有实例所共享的。对于声明为MyClass<T>的类，访问其中的静态变量的方法仍然是 MyClass.myStaticVar。不管是通过new MyClass<String>还是new MyClass<Integer>创建的对象，都是共享一个静态变量。
泛型的类型参数不能用在Java异常处理的catch语句中。因为异常处理是由JVM在运行时刻来进行的。由于类型信息被擦除，JVM是无法区分两个异常类型MyException<String>和MyException<Integer>的。对于JVM来说，它们都是 MyException类型的。也就无法执行与异常对应的catch语句。

34. 解析XML的几种方式的原理与特点：DOM、SAX、JDOM、DOM4J
XML是一种通用的数据交换格式,它的平台无关性、语言无关性、系统无关性、给数据集成与交互带来了极大的方便。
XML在不同的语言环境中解析方式都是一样的,只不过实现的语法不同而已。
XML的解析方式分为四种：1、DOM解析；2、SAX解析；3、JDOM解析；4、DOM4J解析。
其中前两种属于基础方法，是官方提供的平台无关的解析方式；后两种属于扩展方法，它们是在基础的方法上扩展出来的，只适用于java平台。
DOM的全称是Document Object Model，也即文档对象模型。
在应用程序中，基于DOM的XML分析器将一个XML文档转换成一个对象模型的集合（通常称DOM树），应用程序正是通过对这个对象模型的操作，来实现对XML文档数据的操作。
优点：
1、形成了树结构，有助于更好的理解、掌握，且代码容易编写。
2、解析过程中，树结构保存在内存中，方便修改。
缺点：
1、由于文件是一次性读取，所以对内存的耗费比较大。
2、如果XML文件比较大，容易影响解析性能且可能会造成内存溢出。
SAX的全称是Simple APIs for XML，也即XML简单应用程序接口。
与DOM不同，SAX提供的访问模式是一种顺序模式，这是一种快速读写XML数据的方式。
当使用SAX分析器对XML文档进行分析时，会触发一系列事件，并激活相应的事件处理函数，应用程序通过这些事件处理函数实现对XML文档的访问，因而SAX接口也被称作事件驱动接口。
优点：
1、采用事件驱动模式，对内存耗费比较小。
2、适用于只处理XML文件中的数据时。
缺点：
1、编码比较麻烦。
2、很难同时访问XML文件中的多处不同数据。
JDOM解析
特征：
1、仅使用具体类，而不使用接口。
2、API大量使用了Collections类。
DOM4J解析
特征：
1、JDOM的一种智能分支，它合并了许多超出基本XML文档表示的功能。
2、它使用接口和抽象基本类方法。
3、具有性能优异、灵活性好、功能强大和极端易用的特点。
4、是一个开放源码的文件

35. Java1.7与1.8,1.9,10 新特性
1.5
1.自动装箱与拆箱：
2.枚举(常用来设计单例模式)
3.静态导入
4.可变参数
5.内省
1.6
1.Web服务元数据
2.脚本语言支持
3.JTable的排序和过滤
4.更简单,更强大的JAX-WS
5.轻量级Http Server
6.嵌入式数据库 Derby
1.7
1，switch中可以使用字串了
2.运用List tempList = new ArrayList<>(); 即泛型实例化类型自动推断
3.语法上支持集合，而不一定是数组
4.新增一些取环境信息的工具方法
5.Boolean类型反转，空指针安全,参与位运算
6.两个char间的equals
7.安全的加减乘除
8.map集合支持并发请求，且可以写成 Map map = {name:"xxx",age:18};
1.8
1. 允许在接口中有默认方法实现
2. Lambda表达式
3. 函数式接口
4. 方法和构造函数引用
5. Lambda的范围
6. 内置函数式接口
7. Streams
8. Parallel Streams
9. Map
10. 时间日期API
11. Annotations
1.9
1. Jigsaw 项目;模块化源码
2. 简化进程API
3. 轻量级 JSON API
4. 钱和货币的API
5. 改善锁争用机制
6. 代码分段缓存
7. 智能Java编译, 第二阶段
8. HTTP 2.0客户端
9. Kulla计划: Java的REPL实现

36. 设计模式：单例、工厂、适配器、责任链、观察者等等

37. JNI的使用

38. AOP是什么
AOP（Aspect-Oriented Programming，面向方面编程）
它利用一种称为“横切”的技术，剖解开封装的对象内部，并将那些影响了多个类的公共行为封装到一个可重用模块，并将其名为 “Aspect”，即方面。
所谓“方面”，简单地说，就是将那些与业务无关，却为业务模块所共同调用的逻辑或责任封装起来，便于减少系统的重复代码，降低 模块间的耦合度，并有利于未来的可操作性和可维护性。
AOP代表的是一个横向的关系，如果说“对象”是一个空心的圆柱体，其中封装的是对象的属性和行为； 那么面向方面编程的方法，就仿佛一把利刃，将这些空心圆柱体剖开，以获得其内部的消息。而剖开的切面，也就是所谓的“方面”了。
使用“横切”技术，AOP把软件系统分为两个部分：核心关注点和横切关注点。
AOP的核心思想就是“将应用程序中的商业逻辑同对其提供支持的通用服务进行分离。”
实现AOP的技术，主要分为两大类：一是采用动态代理技术，利用截取消息的方式，对该消息进行装饰，以取代原有对象行为的执行；二是采用静态织入的 方式，引入特定的语法创建“方面”，从而使得编译器可以在编译期间织入有关“方面”的代码。

39. OOP是什么
面向对象编程（英文Object Oriented Programming），所以也叫做OOP
OOP的三大特征 封装 多态 继承
封装不用关注具体实现，更专注于自己的代码编写
继承增加代码的可重用性，拓展，修改
多态对已存在的代码具有可替换性

40. AOP与OOP的区别
OOP是面向对象编程，核心思想是将客观存在的不同事物抽象成相互独立的类，然后把与事物相关的属性和行为封装到类里，并通过继承和多态来定义类彼此间的关系，最后通过操作类的实例来完成实际业务逻辑的功能需求。
AOP是面向切面编程，核心思想是将业务逻辑中与类不相关的通用功能切面式的提取分离出来，让多个类共享一个行为，一旦这个行为发生改变，不必修改类，而只需要修改这个行为即可。
  面向目标不同：简单来说OOP是面向名词领域，AOP面向动词领域。
  思想结构不同：OOP是纵向结构，AOP是横向结构。
  注重方面不同：OOP注重业务逻辑单元的划分，AOP偏重业务处理过程的某个步骤或阶段。
两者之间是一个相互补充和完善的关系。

