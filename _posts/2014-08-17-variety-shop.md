---
layout: post
title:  "杂货店"
subtitle: "来来往往，想到就记录一点。"
project: true
tags: [杂货]
bigimg: https://upload.wikimedia.org/wikipedia/commons/4/48/Window_shopping_in_Cowbridge_-_geograph.org.uk_-_1770681.jpg
comments: true
---
**[999]** Windows 系统批量修改文件名

``` 
ren *.java *.txt
```

**[998]** Appsrver 2.6.0 运行初始异常

C:\Windows\System32\drivers\etc 目录下 hosts 文件，去除 127.0.0.1 localhost 前的 # 符号。
    
**[997]** 解决 MySQL Error 1366

办法1：停止 MySQL 服务，修改 my.ini 文件，修改其中的 [mysql] default-character-set=utf8 和 [mysqld] default-character-set=utf8

办法2：打开 MySQL 命令行客户端，在输入命令前输入`SET NAMES UTF8;`
    
**[996]** 绝对路径问题
    
``` html
<a href="<%=request.getContextPath()%>/XXX.jsp"> <!-- 这样获得的是绝对路径 -->
<a href="XXX.jsp"> <!-- 这样获得的是相对路径 -->
```

``` java
String contextPath = request.getContextPath(); // 项目名
String serverPort = request.getServerPort();
String serverName = request.getServerName();
String scheme = request.getScheme();
String basePath = scheme + "://" + serverName + ":" + serverPort + contextPath + "/"; // 完整路径
```

**[995]** JavaScript 复制文本至粘贴板
    
``` javascript
function copywenben() {
    var content = document.getElementById("content").innerText;
    window.clipboardData.setData("Text", content);
}
```

**[994]** 更改 MyEclipse 中 jsp 的默认打开方式

window---preferences---general---editor---file associations---找到 jsp 类型，选择默认打开方式为 jsp editor
    
**[993]** 更改 MyEclipse 中 Java 的编译版本

`Syntax error, parameterized types are only available if source level is 1.5`

Window---preferences---Java---compiler---更改 Compiler compliance level

右击项目，修改properties，找到 java compiler 修改 compiler compliance level

**[992]** PHP 页面抓取

``` php
if($keyword=="Welcome") {
	include('simple_html_dom.php'); // 载入库文件，simple_html_dom 解析器，可以直接调用来解析HTML标签
	$html = file_get_html('http://www.xxx.com/'); //
	foreach($html->find('div#score_6') as $ret) // 标签
	$contentStr = $ret->plaintext; // 读取文本
	$contentStr = trim($contentStr); // 删除空格
	$resultStr = sprintf($newsTpl, $fromUsername, $toUsername, $time, $contnetStr);
	echo $resultStr;
}
```

**[991]** JavaScript 在文档流中写入内容 document.write

``` javascript
document.write("<ul><li><input type="checkbox">a</li></ul>");
```

**[990]** 避免过度使用 null 值

``` java
Optional<String> optionalString = Optional.ofNullable(nullableString);
if(optionalString.isPresent()) {
    System.out.println(optionalString.get());
} 
```

``` java
// Java 8 Lambda
Optional<String> optionalString = Optional.ofNullable(nullableString);
optionalString.ifPresent(System.out::println);
```

**[989]** 强调某个异常不重要的一个简便途径就是将此信息作为异常的变量名

``` java
try {
    doSomethingHere();
} catch (NullPointerException unimportant) {}
```

**[988]** Java 使用 Iterator.remove 方法更新集合中的内容，不需要创建额外的集合，可以节省空间，同时避免并发异常

``` java
Iterator<String> strIterator = oneStrList.iterator();
while(strIterator.hasNext()) {
    String str = strIterator.next();
    if("rm".equalsIgnoreCase(str)) {
        strIterator.remove();
    }
}
```

``` java
ListIterator<String> strIterator = oneStrList.listIterator();
while (strIterator.hasNext()) {
    String str = strIterator.next();
    if("mv".equalsIgnoreCase(str)) {
        strIterator.remove();
        strIterator.add("new"); /* 或者使用 strIterator.set("new"); */
    }
}
```

``` java
// Java 8 Method
oneStrList.removeIf(String::isEmpty);
```

**[987]** Java8 可以将一个 collection 转换为 stream，并且根据条件过滤 stream

``` java
List<String> anotherStrList = oneStrList.stream()
                .filter((str -> !"rm".equalsIgnoreCase(str)))
                .collect(Collectors.toCollection(ArrayList::new));
```

``` java
List<String> anotherStrList = oneStrList.stream().peek(str -> {
            if("rm".equalsIgnoreCase(str)) {
                oneStrList.remove(str);
            }
        }).collect(Collectors.toCollection(ArrayList::new));

```

``` java
// 针对 stream 的一般准则是，在查询 stream 的时候，避免修改后端的 collection，避免产生并发异常
List<String> anotherStrList = oneStrList.stream().peek(str -> {
    if("rm".equalsIgnoreCase(str)) {
        oneStrList.remove(str);
    }
}).collect(Collectors.toCollection(ArrayList::new));
```

**[986]** 用 CopyOnWriteArrayList、CopyOnWriteSet、ConcurrentHashMap 等特殊的集合处理，缺点是每次修改都产生一个新的集合。

**[985]** 对象的两个准则

- 如果两个对象相等，那么 hashCode 必须相等。
- 如果两个对象有相同的 hashCode 那么它们可能相等也可能不相等。

**[984]** 语法糖

语法糖（Syntactic sugar），也译为糖衣语法，是由英国计算机科学家彼得·约翰·兰达（Peter J. Landin）发明的一个术语，指计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用。

通常来说使用语法糖能够增加程序的可读性，从而减少程序代码出错的机会。

**[983]** 鸭子类型

一只鸟走起来像鸭子、游起泳来像鸭子、叫起来也像鸭子，那它就可以被当做鸭子。也就是说，它不关注对象的类型，而是关注对象具有的行为（方法）。

**[982]** Java ObjectIO
- transient 变量修饰符，用该关键字标记的成员变量不参与序列化过程。
- Serializable 序列化。
- Externalizable 可控制的序列化。

**[981]** 多态
- 要有继承（extends）
- 要有重写（override）
- 父类引用要指向子类对象（A a = new B()）

**[980]** boolean result = obj instanceof oneClass 类型判断，运算符

**[979]** PHP 数据库操作
``` php
<?php
$link=mysql_connect('localhost','root','123456');
mysql_select_db('threephp');
mysql_set_charset('utf8');
$sql="SELECT id,name,age,sex FROM user WHERE id=10";
$res=mysql_query($sql);
$row=mysql_fetch_assoc($res);
var_dump($row);
?>
```

**[978]** URL，UniformResourceLocator，统一资源定位符。

**[977]** 区别动态网站和静态网站最基本的方法，通常是区别是否采用了数据库的开发模式。

**[976]** 建设一个完整的动态网站所需要的 Web 构件
1. 客户端浏览器：Web 标准。
2. 可扩展的超文本标记语言 XHTML。
3. 层叠样式表 CSS 标记语言解释执行。
4. XML 和 XSLT。
5. 客户端脚本编程语言：JavaScript。
6. Web 服务器：保存网页的远程服务器 Apache。
7. 服务器端脚本编程语言：PHP，超文本预处理器。
8. 数据库管理系统：MySQL。

**[975]** 开发技术架构
- ASP.NET：Windows，IIS，SQLServer，ASP。
- J2EE：UNIX，Tomcat / WebLogic，Oracle，JSP。
- LAMP：Linux，Apache，MySQL，PHP。

**[974]** 破解Adobe的CS5系列软件

C:\Windows\System32\drivers\etc
```
127.0.0.1       activate.adobe.com
127.0.0.1       practivate.adobe.com
127.0.0.1       ereg.adobe.com
127.0.0.1       activate.wip3.adobe.com
127.0.0.1       wip3.adobe.com
127.0.0.1       3dns-3.adobe.com
127.0.0.1       3dns-2.adobe.com
127.0.0.1       adobe-dns.adobe.com
127.0.0.1       adobe-dns-2.adobe.com
127.0.0.1       adobe-dns-3.adobe.com
127.0.0.1       ereg.wip3.adobe.com
127.0.0.1       activate-sea.adobe.com
127.0.0.1       wwis-dubc1-vip60.adobe.com
127.0.0.1       activate-sjc0.adobe.com
```

**[973]** HTML 中 border、margin、padding 的四个方向数值顺序为上右下左。

**[972]** CSS 属性 z-index 参数值越大，则被层叠在最上面。

**[971]** 标签`<a>`和 CSS 属性 display:block 适合在一起使用。

**[970]** HTML 页面加载完才会执行 onload 事件，适用于延迟加载。

**[969]** 标签`<frameset>`与 HTML 标签`<body>`不能一起使用

因为`<frameset>`是一种框架结构，会将整个页面占据掉，所以不要用`<body>`进行布局，并确保 DOCTYPE 被设置为 "Frameset DTD"。

**[968]** CSS 属性 display 的值的说明

- block（块对象的默认值）将对象强制作为块对象呈递，为对象之后添加新行。

- inline（内联对象的默认值）将对象强制作为内联对象呈递，从对象中删除行。

- inline-block（将对象呈递为内联对象，但是对象的内容作为块对象呈递）旁边的内联对象会被呈递在同一行内。

- none（隐藏对象）与 visibility 属性的hidden值不同，其不为被隐藏的对象保留其物理空间。

**[967]** CSS 属性 required 适用于以下类型的`<input>`标签

text、search、url、telephone、email、password、date pickers、number、checkbox、radio和file。

**[966]** 在`<canvas>`标签中定义 width、height 跟在 CSS style 中定义 width、height 是不同的

`<canvas>`标签的 width 和 height 是画布实际宽度和高度，就是在这个上面绘制图形。

CSS style 的 width 和 height 是`<canvas>`画布在浏览器中被渲染的高度和宽度。

如果`<canvas>`的 width 和 height 没指定或值不正确，就被设置成默认值(width:300px，height:150px)。

**[965]** CSS 属性 position，绝对定位和相对定位

absolute(绝对定位):

1. 如果没有父级DIV，则会根据浏览器原始点去定位，而且跟他相邻的DIV会忽略它，定位后则可用TRBL(top,right,bottom,left)去布局。注意：TRBL必须在设定了position后方可用。
2. 如果有父级DIV，那还要看父级DIV是否有position和padding设置,如果有，则此DIV会根据父DIV去定位，且有“撑开或占据高度”的作用，如果没有，则跟上面没有区别。

relative(相对定位):
    
1. 如果没有父级DIV，它和absolute没有区别。顶级DIV最好设成relative定位。
2. 如果有父级DIV，但父级没设置position，那还是根据body内容区去定位的。如果顶级DIV用relative定位则放小窗口也不会影响页面的,但absolute就会全部重叠。
3. 如果父级设置了position或者padding属性后，则此DIV会根据父DIV去定位。而且有“撑开或占据高度”的作用。

**[964]** CSS 中!important的用法
``` css
#main {
    width:100px!important;
    width:10px;
    border:1px solid #000;
}
```
!important是CSS1就定义的语法，是用于提高指定样式规则的应用优先级。

IE一直都不支持这个语法，而其他的浏览器都支持，可以利用它来分别给火狐浏览器和IE浏览器样式不同的定义，只要在样式后面加上!important。

**[963]** MyEclipse 的 Servers 视图出现 Could not create the view: An unexpected exception was thrown.警告的解决办法

到工作空间下面的workspaces\.metadata\.plugins\org.eclipse.core.runtime\.settings目录下面找到了名称为com.genuitec.eclipse.ast.deploy.prefs文件，将此文件删除，重启MyEclipse。

**[962]** 配置ADT环境变量

打开PATH，添加变量值：C:\android-sdk-windows\tools

**[961]** Android 的 adb 命令

| 命令 | 用途 |
|:--------|--------:|
| android | 列出Android SDK 和 AVD Manager |
|----
| android list avds | 列出所有创建的模拟器 |
|----
| android list targets | 列出所有的SDK可用版本 |
|----
| adb devices | 列出所有可装载的设备 |
|----
| adb shell | 进入设备的根目录 |
|----
| adb installs <AVD名称> <APK文件所在位置> | 安装应用 |
|----
| emulator -avd <AVD名称> | 启动模拟器 |
|----
| mksdcard 256M <d:/sdcard.img> | 将在D盘下生成256M的sdcard |
|----
| adb pull <remote> <local>/adb push <local> <remote> | 取出/存放文件 |
|----
| android create avd --name <AVD名称> --target <SDK版本> | 创建模拟器 |
|=====
| 命令 | 用途 |
{: rules="groups"}

**[960]** NoSQL (NoSQL = Not Only SQL)，意即“不仅仅是 SQL”。

**[959]** 传统的关系数据库管理系统（RDBMSs）遵循ACID规则。
- A (Atomicity) 原子性
- C (Consistency) 一致性
- I (Isolation) 独立性
- D (Durability) 持久性

**[958]** 运算
``` java
int a = 2147483647 * 2; // -2
float a = 0.1f * 0.1f; // 0.010000001
double a = 0.1 * 0.1; // 0.010000000000000002
```

**[957]** 自增(`++`)和自减(`--`)
- 放在变量前，先用原来的值进行其他操作，然后在对自己做修改。
- 放在变量后，先对自己做修改，再用修改后的值进行其他操作。

``` java
b = a++-1; // b = a-1; a = a+1;
```

``` java
b = ++a-1; // a = a+1; b = a-1;
```

**[956]** char本质上是一个固定占用两个字节的无符号正整数，对应于Unicode编号，只能表示Unicode编号在65536以内的字符。

**[955]** AspectJ 的切入点表达式
1. `execution(* *(..))`

    表示匹配所有方法
2. `execution(public * org.superf.service.OneService.*(..))`

    表示匹配org.superf.service.OneService中所有的公有方法
3. `execution(* org.superf.service..*.*(..))`

    表示匹配org.superf.service包及其子包下的所有方法

**[954]** 程序最终都是一条条的指令。

**[953]** 跳转指令：条件跳转、无条件跳转。

**[952]** 可变长度参数(String... str)实际上会转换为数组参数。

**[951]** 实现了`java.io.Serializable`接口的单例类，在复原这个单例类的序列化对象时，可能会产生多个实例。

可以通过提供 readResolve 方法解决

``` java
private Object readResolve() throws ObjectStreamException {
    return instance;
}
```

**[950]** 栈一般是从高位地址向低位地址扩展，即栈底的内存地址最高，栈顶的内存地址最低。

**[949]** 在父类构造方法中调用可被子类重写的方法是一种不好的实践。

因为在 new 的过程中，首先初始化父类，父类构造方法中调用该被子类重写的方法，就会调用子类方法，子类方法如果访问子类的实例变量，而这个时候子类的实例变量的赋值语句和构造方法还没有执行，所以会输出异常。

**[948]** 每次函数调用都需要分配额外的栈空间用于存储参数、局部变量以及返回地址，需要进行额外的入栈和出栈操作。

递归调用之类可能会导致栈空间过深、系统会抛出`java.lang.StackOverflowError`，即栈溢出。

**[947]** 方法中的参数和定义的变量，都分配在栈中，这些只有在方法被调用时才分配，而且调用结束后就释放。

对于数组，在栈中存放的是实际内容的地址0x1000，存放地址的栈空间会随着入栈分配，出栈释放，但存放实际内容的堆空间不受影响。当栈空间再也没有变量指向它的时候，Java 系统会自动进行垃圾回收，从而释放这块空间。

**[946]** 变量对应的内存地址和内存内容

| 代码 | 内存地址 | 内存数据 |
|:--------|:--------|:--------|
| int a = 100; | 1000 | 100 |
|----
| int[] arr = {1, 2, 3}; | 2000 | 3000 |
|----
|  | 3000 | 1 |
|----
|  | 3004 | 2 |
|----
|  | 3008 | 3 |
|=====
{: rules="groups"}

**[945]** Java中的实例变量、静态变量、静态方法、private方法，都是静态绑定的。

**[944]** MySQL 事件（EVENT），七天删除一次数据
``` sql
-- 先查看event是否已经开启
SHOW VARIABLES LIKE 'event_scheduler';
-- 若event是OFF状态，则开启event
SET GLOBAL event_scheduler = 1;
-- 每周执行一次删除超过七天的数据
CREATE EVENT delete_data_weekly 
ON SCHEDULE EVERY 7 DAY STARTS '2016-09-16 8:30:00' 
DO 
DELETE FROM redis_change_record WHERE DATE(TIMESTAMP) <= DATE_SUB(CURDATE(), INTERVAL 7 DAY)

-- 查看任务状态，便于修改
SELECT * FROM mysql.event;
```

**[943]** MySQL 将时间戳格式化
``` sql
DATE_FORMAT(FROM_UNIXTIME(insert_timestamp/1000), '%Y%m%d') 
```

**[942]** MySQL 添加索引
``` sql
ALTER TABLE answer_record ADD INDEX userIdKey(userId)
```

**[941]** MySQL 语句优化
``` sql
EXPLAIN
SELECT * FROM answer_record AS t1
inner join (SELECT * FROM answer_record WHERE endtime>0) t2 
on t1.userId = t2.userId
```

**[940]** MySQL Error 1449: `The user specified as a definer ('root'@'%') does not exist`

权限问题导致的，需要授权给 root 账号所有 sql 权限

``` sql
mysql> grant all privileges on *.* to root@"%" identified by ".";
Query OK, 0 rows affected (0.00 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

**[939]** 解决 IDEA 中构建 MAVEN 骨架速度慢的问题，可以在 Properties 中添加参数

|...|...|
|:--------|:--------|
| archetypeCatalog | internal |
|=====
|...|...|
{: rules="groups"}
 
表示插件使用 archetype 元数据从本地获取，默认为 remote，local，即从中央仓库获取，指定internal来表示仅使用内部元数据。

**[938]** Java 泛型中的标记符含义

- E - Element (在集合中使用，因为集合中存放的是元素)

- T - Type（Java 类）

- K - Key（键）

- V - Value（值）

- N - Number（数值类型）

- ? -  表示不确定的java类型

**[937]** `getDeclaredField()`可以获取一个类的所有字段，`getField()`只能获取类的 public 字段。

**[936]** 提示`com.sun.management.OperatingSystemMXBean`无法引入

Eclipse默认把这些受访问限制的API设成了ERROR。

Windows---Preferences---Java Complicer---Errors/Warnings---Deprecated and restricted API，将其中的Forbidden references(access rules)设置为Warning。

**[935]** Eclipse 在启动发生 An internal error occurred during: "Initializing Java Tooling" 错误

删除workspace\.metadata\.plugins\org.eclipse.core.resources\.projects目录，之后重启Eclipse即可。

**[934]** Java Object 方法

`clone(), equals(), hashCode(), toString(), notify(), notifyAll(), wait(), finalize(), getClass()`

**[933]** 《Java虚拟机规范》

> “虽然定义了boolean这种数据类型，但是只对它提供了非常有限的支持。在Java虚拟机中没有任何供boolean值专用的字节码指令，Java语言表达式所操作的boolean值，在编译之后都使用Java虚拟机中的int数据类型(4 bytes)来代替，而boolean数组将会被编码成Java虚拟机的byte数组，每个元素boolean元素占8位(1 bytes)”。

这样可以得出boolean类型单独使用是4个字节，在数组中又是1个字节。

**[932]** TreeMap, LinkedHashMap, HashMap的区别
- HashMap的底层实现是散列表，内部的元素是无序的；
- TreeMap的底层实现是红黑树，内部的元素的有序的。排序的依据是自然序或者是创建TreeMap时所提供的比较器（Comparator）对象。
- LinkedHashMap，能够记住插入元素的顺序的HashMap。

**[931]** 只有两种情况finally块中的语句不会被执行
- 调用了System.exit()方法
- JVM崩溃

因此，如果try语句块中包含return语句，finally语句块会执行。

**[930]** 异常
- Throwable类，基类
- Error类，无法控制的内部错误
- Exception类，异常
- RuntimeException及其子类，未检查异常（如NPE，应该避免未检查异常的发生）
- IOException及其子类，已检查异常（编译器会检查，若没有则会报错）

**[929]** 静态内部类与非静态内部类的区别

静态内部类不会持有外部类的引用，而非静态内部类会隐式持有外部类的引用。

**[928]** `volatile`关键字，可以使 Java Memory Model 保证对同一个 volatile 关键字定义的变量的写操作 happens-before（先行发生）对它的读操作。

**[927]** `ReentrantLock`，支持两种获取锁的方式，一种是公平模型，一种是非公平模型。
- 公平锁模型，初始化时，state=0，表示无人抢占锁；线程A取得了锁，把state原子性+1，这时候state被改为1，A线程继续执行其他任务，然后线程B请求锁，无法获取，生成节点（初始化的时候，会生成一个空的头节点，然后才是B线程节点）进行排队；当A再次请求锁时，就相当于是有特权的，这时候state再次+1（就是一个线程在获取了锁之后，再次去获取了同一个锁，这时候仅仅是把状态值进行累加。如果线程A释放了一次锁，仅仅是把状态值减了，只有线程A把此锁全部释放了，状态值减到0了，其他线程才有机会获取锁）；当一个线程节点被唤醒然后取得了锁，对应节点会从队列中删除。
- 非公平模型，即当线程A执行完之后，要唤醒线程B是需要时间的，而且线程B醒来后还要再次竞争锁，所以如果在切换过程当中，来了一个线程C，那么线程C是有可能获取到锁的，如果C获取到了锁，B就只能继续乖乖休眠了。

**[926]** Java 四种引用类型，代表了 JVM 回收内存的四种强度
- 强引用，`Object o = new Object();`，可以直接访问目标对象，所指向的对象在任何时候都不会被系统回收（JVM宁愿抛出OOM异常，也不会回收强引用所指向的对象，可能导致内存泄漏，在使用完成之后可以把对象设置为null，集合的话可以使用Collection.clear()）。
- 软引用，`SoftReference<Object> softRef = new SoftReference<Object>(o);`，如果内存空间不足，就会回收这些对象的内存。只要垃圾回收器没有回收它，该对象就可以被程序使用。软引用可用来实现内存敏感的高速缓存。  
- 弱引用，`WeakReference<Object> weakRef = new WeakReference<Object>(o);`，垃圾回收器线程不管当前内存空间足够与否，都会回收它的内存。由于垃圾回收器是一个优先级很低的线程，因此发现弱引用的对象不会很快。用于对象偶尔使用，并希望随时能获取，但又不想影响此对象的垃圾收集。
- 虚引用，`ReferenceQueue<Object> rq = new ReferenceQueue<Object>(); PhantomReference<Object> phantomRef = new PhantomReference<Object>(o, rq);`，无法通过虚引用来获取对一个对象的真实引用，唯一的用处，能在对象被GC时收到系统通知。

**[925]** `ThreadLocal`的作用是提供线程内的局部变量，在多线程环境下访问时能保证各个线程内的`ThreadLocal`变量各自独立。

**[924]** `CountDownLatch`允许线程集等待直到计数器为0。适用场景，当一个或多个线程需要等待指定数目的事件发生后再继续执行。

**[923]** 使用`for-each`循环与常规的`for`循环在有些场合下能带来微小的性能提升，因为它只计算一次数组索引的上限。

**[922]** 为了克服函数式接口的这种脆弱性并且能够明确声明接口作为函数式接口的意图，Java 8增加了一种特殊的注解@FunctionalInterface（Java 8中所有类库的已有接口都添加了@FunctionalInterface注解）。

**[921]** 哈希表是由数组和链表组成，具有较快（常量级）的查询速度，及相对较快的增删速度，所以很适合在海量数据的环境中使用。

**[920]** 由于`Hashtable`是采用`synchronized`进行同步，相当于所有线程进行读写时都去竞争一把锁，导致效率非常低下。`ConcurrentHashMap`可以做到读取数据不加锁，并且其内部的结构可以让其在进行写操作的时候能够将锁的粒度保持地尽量地小，不用对整个`ConcurrentHashMap`加锁。

**[919]** 编写工具类的规范
- 将类定义为`final class`
- 只定义一个无参的构造函数且抛出断言错误，防止被反射调用
``` java
private Objects() {
    throw new AssertionError("No java.util.Objects instances for you!");
}
````
- 方法都是静态方法
- 静态方法中只抛出未检查异常

**[918]** 获取 HashCode 值
``` java
int hashCode = Objects.hashCode(s1); // 根据 内容 来产生 hash 值
int identityHashCode = System.identityHashCode(s1); // 根据 内存地址 来产生 hash 值
``` 

**[917]** 实时获取有性能问题的 SQL 
``` sql
SELECT id,user,host,DB,command,time,state,info
FROM information_schema.processlist
WHERE time >= 60;
```
查询当前服务器执行超过 60s 的SQL，可以通过脚本周期性的来执行，查出有问题的 SQL。

**[916]** 以太网数据包（packet）的大小是固定的，最初是1518字节，后来增加到1522字节。其中， 1500 字节是负载（payload），22字节是头信息（head）。

IP 数据包在以太网数据包的负载里面，它也有自己的头信息，最少需要20字节，所以 IP 数据包的负载最多为1480字节。

TCP 数据包在 IP 数据包的负载里面。它的头信息最少也需要20字节，因此 TCP 数据包的最大负载是 1480 - 20 = 1460 字节。由于 IP 和 TCP 协议往往有额外的头信息，所以 TCP 负载实际为1400字节左右。

因此，一条1500字节的信息需要两个 TCP 数据包。HTTP/2 协议的一大改进， 就是压缩 HTTP 协议的头信息，使得一个 HTTP 请求可以放在一个 TCP 数据包里面，而不是分成多个，这样就提高了速度。

**[915]** 一个包1400字节，如果一次性发送大量数据，就必须分成多个包。比如，一个 10MB 的文件，需要发送7100多个包。

发送的时候，TCP 协议为每个包编号（sequence number，简称 SEQ），以便接收的一方按照顺序还原。万一发生丢包，也可以知道丢失的是哪一个包。
         
第一个包的编号是一个随机数。假定这个包的负载长度是100字节，那么可以推算出下一个包的编号应该是101。这就是说，每个数据包都可以得到两个编号：自身的编号，以及下一个包的编号。接收方由此知道，应该按照什么顺序将它们还原成原始文件。
         
当前包的编号是45943，如果下一个数据包的编号是46183，由此可知，这个包的负载是240字节。

**[914]** 收到 TCP 数据包以后，组装还原是操作系统完成的。应用程序不会直接处理 TCP 数据包。
          
对于应用程序来说，不用关心数据通信的细节。除非线路异常，收到的总是完整的数据。应用程序需要的数据放在 TCP 数据包里面，有自己的格式（比如 HTTP 协议）。

**[913]** TCP 并没有提供任何机制，表示原始文件的大小，这由应用层的协议来规定。比如，HTTP 协议就有一个头信息Content-Length，表示信息体的大小。对于操作系统来说，就是持续地接收 TCP 数据包，将它们按照顺序组装好，一个包都不少。
          
操作系统不会去处理 TCP 数据包里面的数据。一旦组装好 TCP 数据包，就把它们转交给应用程序。TCP 数据包里面有一个端口（port）参数，就是用来指定转交给监听该端口的应用程序。

**[912]** 服务器发送数据包，当然越快越好，最好一次性全发出去。但是，发得太快，就有可能丢包。带宽小、路由器过热、缓存溢出等许多因素都会导致丢包。线路不好的话，发得越快，丢得越多。

TCP 协议为了做到效率与可靠性的统一，设计了一个慢启动（slow start）机制。开始的时候，发送得较慢，然后根据丢包的情况，调整速率：如果不丢包，就加快发送速度；如果丢包，就降低发送速度。

**[911]** Linux 内核里面设定了（常量TCP_INIT_CWND），刚开始通信的时候，发送方一次性发送10个数据包，即"发送窗口"的大小为10。然后停下来，等待接收方的确认，再继续发送。
          
默认情况下，接收方每收到两个 TCP 数据包，就要发送一个确认消息。"确认"的英语是 acknowledgement，所以这个确认消息就简称 ACK。
          
ACK 携带两个信息。
- 期待要收到下一个数据包的编号
- 接收方的接收窗口的剩余容量

发送方有了这两个信息，再加上自己已经发出的数据包的最新编号，就会推测出接收方大概的接收速度，从而降低或增加发送速率。这被称为"发送窗口"，这个窗口的大小是可变的。

即使对于带宽很大、线路很好的连接，TCP 也总是从10个数据包开始慢慢试，过了一段时间以后，才达到最高的传输速率。这就是 TCP 的慢启动。

**[910]** TCP 协议可以保证数据通信的完整性，每一个数据包都带有下一个数据包的编号。如果下一个数据包没有收到，那么 ACK 的编号就不会发生变化。
          
举例来说，现在收到了4号包，但是没有收到5号包。ACK 就会记录，期待收到5号包。过了一段时间，5号包收到了，那么下一轮 ACK 会更新编号。如果5号包还是没收到，但是收到了6号包或7号包，那么 ACK 里面的编号不会变化，总是显示5号包。这会导致大量重复内容的 ACK。
          
如果发送方发现收到三个连续的重复 ACK，或者超时了还没有收到任何 ACK，就会确认丢包，即5号包遗失了，从而再次发送这个包。通过这种机制，TCP 保证了不会有数据包丢失。

**[909]** HTTP 协议基于 TCP/IP 协议族，它没有加密机制。但可以通过 SSL（Secure Socket Layer，安全套接层）建立安全的通信线路，再进行 HTTP 通信，这种与 SSL 结合使用的称为 HTTPS（HTTP Secure，超文本传安全协议）。

**[908]** 1996 年在 RFC 1945 中定义了 HTTP 1.0 规范，仅 60 页，到 1999 年在 RFC 2616 定义了 HTTP 1.1，增长到了 176 页。但是，随着 web 技术的飞速发展。 HTTP 1.1 已经无法满足用户对性能的要求，此后 Google 推出协议 SPDY，意在解决 HTTP 1.1 中广为人知的性能问题。SPDY 得到了 Chrome、Firefox 和 Opera 的支持，很多大型网站（如谷歌、Twitter、Facebook）都对兼容客户端使用 SPDY。SPDY 在被行业采用并证明能够大幅提升性能之后，已经具备了成为一个标准的条件。HTTP/2 是 HTTP 协议自 1999 年 HTTP 1.1 发布后的首个更新，主要基于 SPDY 协议。它由互联网工程任务组（IETF）的Hypertext Transfer Protocol Bis（httpbis）工作小组进行开发。该组织于2014年12月将HTTP/2标准提议递交至IESG进行讨论，于2015年2月17日被批准。HTTP/2标准于2015年5月以RFC 7540正式发表。
- HTTP/2 采用二进制格式传输数据，而非 HTTP 1.x 的文本格式，二进制协议解析起来更高效。             
- HTTP/1 的请求和响应报文，都是由起始行，首部和实体正文（可选）组成，各部分之间以文本换行符分隔。HTTP/2 将请求和响应数据分割为更小的帧，并且它们采用二进制编码。                                  
- HTTP/2 中，同域名下所有通信都在单个连接上完成(多路复用)，这个连接可以承载任意数量的双向数据流。每个数据流都以消息的形式发送，而消息又由一个或多个帧组成。多个帧之间可以乱序发送，因为根据帧首部的流标识可以重新组装。

**[907]** HTTP 1.x 中，如果想并发多个请求，必须使用多个 TCP 链接，且浏览器为了控制资源，还会对单个域名有 6-8 的个数限制。

在 HTTP/2 中，有了二进制分帧之后，HTTP/2 不再依赖 TCP 链接去实现多流并行了，在 HTTP/2中：
- 同域名下所有通信都在单个连接上完成。同个域名只需要占用一个 TCP 连接，消除了因多个 TCP 连接而带来的延时和内存消耗。
- 单个连接可以承载任意数量的双向数据流。单个连接上可以并行交错的请求和响应，之间互不干扰。
- 数据流以消息的形式发送，而消息又由一个或多个帧组成，多个帧之间可以乱序发送，因为根据帧首部的流标识可以重新组装。
- 在HTTP/2中，每个请求都可以带一个31bit的优先值，0表示最高优先级， 数值越大优先级越低。有了这个优先值，客户端和服务器就可以在处理不同的流时采取不同的策略，以最优的方式发送流、消息和帧。

**[906]** “软链接”和“硬链接”的区别
``` shell
$ ln myfile hard
$ ls -li
```
- 硬链接： 与普通文件没什么不同，inode 都指向同一个文件在硬盘中的区块
``` shell
$ ln -s myfile soft
$ ls -li
```
- 软链接： 保存了其代表的文件的绝对路径，是另外一种文件，在硬盘上有独立的区块，访问时替换自身路径。

**[905]** 承载量是分布式系统存在的原因：高吞吐、高并发、低延迟和负载均衡。
分布式系统具有典型的三层结构：接入层、逻辑层、存储层。但是实际上往往不止有三层，根据业务需求，会设计成多个层次。

**[904]** 消息队列
多层结构中为了把请求转交给正确的进程处理，而设计很多专门用于转发请求的进程和服务器（以 Proxy 或者 Router 来命名），通过 TCP 来连接前后两端。TCP 的网络编程复杂且故障后不容易恢复。所以，人们设计出更好进程间通讯机制：消息队列。

**[903]** 类似Map Reduce架构的统计系统，能对海量的日志信息进行快速的统计以及报警。或者直接使用Hadoop系统，还可以使用Kafka来作为日志存储系统，上面再搭建统计程序。

**[902]** 解决分布式系统可管理性的基本手段
- 目录服务（ZooKeeper）
- 消息队列服务（ActiveMQ、ZeroMQ、Jgroups）
- 事务系统（两个核心工具：一个是稳定的状态存储系统；另外一个是方便可靠的广播系统）
- 自动部署工具（Docker）
- 日志服务（log4j）

**[901]** 分布式领域 CAP 理论
- Consistency（一致性）， 数据一致更新，所有数据变动都是同步的
- Availability（可用性），好的响应性能
- Partition tolerance（分区容忍性），可靠性
任何分布式系统只可同时满足二点，没法三者兼顾。

**[900]** 分布式领域 BASE 理论
- Basically Available（基本可用），允许损失部分可用性，保证核心可用
- Soft-state（软状态/柔性事务），允许系统在不同节点间副本同步的时候存在延时
- Eventual Consistency（最终一致性），不需要实时保证系统数据的强一致性
即使无法做到强一致性，但每个业务根据自身的特点，采用适当的方式来使系统达到最终一致性。

**[899]** MapReduce计算模型，Hadoop的并行计算框架，来源于谷歌，是一种基于HDFS的离线计算框架。主要包含五大模块Input，Map，Shuffle，Reduce，Output，其中shuffle过程是MapReduce中最为复杂，也是灵活性最高的一部分。 

**[898]**  int 申请不到奇数地址，因为字节对齐。CPU每次都是从以4字节（32位CPU）或是8字节（64位CPU）的整数倍的内存地址中读进数据。如果不对齐的话，很有可能一个4字节int需要分两次读取。

**[897]** 大小端模式
- 大端模式，是指数据的高字节保存在内存的低地址中，而数据的低字节保存在内存的高地址中，这样的存储模式有点儿类似于把数据当作字符串顺序处理：地址由小向大增加，而数据从高位往低位放；这和我们的阅读习惯一致。
- 小端模式，是指数据的高字节保存在内存的高地址中，而数据的低字节保存在内存的低地址中，这种存储模式将地址的高低和数据位权有效地结合起来，高地址部分权值高，低地址部分权值低。

**[896]** 服务端可以根据http请求头中的User-Agent来检查客户端是否是一个合法的浏览器程序

**[895]** 勿在 Controller 中定义成员变量，如果要定义一个非静态成员变量时候，可以通过注解@Scope("prototype")，将 Controller 设置为多例模式。

**[894]** Form 的 enctype 属性为编码方式
- application/x-www-form-urlencoded 窗体数据被编码为名称/值对。这是标准的编码格式。
- multipart/form-data 窗体数据被编码为一条消息，页上的每个控件对应消息中的一个部分。
- text/plain 窗体数据以纯文本形式进行编码，其中不含任何控件或格式字符。

**[893]** 乐观锁(Optimistic Locking)
- 与悲观锁相对应，会假设数据在极大多数情况下不会形成冲突，因此只有在数据提交的时候，才会对数据是否产生冲突进行检验。如果产生数据冲突了，则返回错误信息，进行相应的处理。
- 使用版本号(version)机制来实现，这是乐观锁最常用的实现方式。

**[892]** 缓存处理
- CDN主要解决将数据缓存到离用户最近的位置，一般缓存静态资源文件（页面，脚本，图片，视频，文件等）。国内网络异常复杂，跨运营商的网络访问会很慢。为了解决跨运营商或各地用户访问问题，可以在重要的城市，部署CDN应用。使用户就近获取所需内容，降低网络拥塞，提高用户访问响应速度和命中率。
- 反向代理位于应用服务器机房，处理所有对WEB服务器的请求。如果用户请求的页面在代理服务器上有缓冲的话，代理服务器直接将缓冲内容发送给用户。如果没有缓冲则先向WEB服务器发出请求，取回数据，本地缓存后再发送给用户。通过降低向WEB服务器的请求数，从而降低了WEB服务器的负载。

**[891]** 定时任务 @Scheduled 注解
- (cron = "0/5 * * * * ?") 每个5秒尝试执行一次。
- (fixedDelay = 5000) 上一个任务结束后间隔5秒执行下一个任务。
- (fixedRate = 5000) 每个5秒的任务都会执行。

**[890]** 在使用 Spring 的 @Cacheable 注解的时候，要注意，如果 类A 的 方法f1() 被标注了 @Cacheable 注解，那么当 类A 的其他方法，例如方法 f2() 去直接调用f1()的时候，@Cacheable 是不起作用的，原因是 @Cacheable 是基于 Spring AOP 代理类，f2() 属于内部方法，直接调用 f1() 时，是不通过代理的。

**[889]** 解决 Lambda 表达式的 tomap方法 空指针问题
``` java
Map<Integer, String> locMap = abList.parallelStream().collect(HashMap::new, (m, v) -> m.put(v.getA(), v.getB()), Map::putAll);
```

**[888]** 对所有的编程语言，最后的目的其实就是两种：提高硬件的运行效率和提高程序员的开发效率。

C语言已经把硬件的运行效率压缩到了极致。

但是这种设计思想带来的问题就是易用性和安全性的缺失。

**[887]** Java 获取服务器操作系统的版本，64 / 32 位。
``` java
System.getProperty("sun.arch.data.model");
```

**[886]** （转）作为用户，可以通过手机号和短信验证码登录，以便于更方便的登录。
- 短信验证码有效期 2 分钟。
- 验证码为 6 位纯数字。
- 每个手机号 60 秒内只能发送一次短信验证码，且这一规则的校验必须在服务器端执行。
- 同一个手机号在同一时间内可以有多个有效的短信验证码。
- 保存于服务器端的验证码，至多可被使用 3 次（无论和请求中的验证码是否匹配），随后立即作废，以防止暴力攻击。
- 短信验证码不可直接记录到日志文件。
- 发送短信验证码之前，先验证图形验证码是否正确（可选）。
- 集成第三方 API 做登录保护（可选）。

**[885]** SQL 行数据转为列数据，列数据转为行数据
``` sql
# 行转列
SELECT * FROM table_name
PIVOT (
    SUM(column_key1) FOR column_key2 IN (column_value1, column_value2, column_value3)
)

# 列转行
SELECT * FROM table_name
UNPIVOT (
    column_key1 FOR column_key2 IN (column_value1, column_value2, column_value3)
)
```
但是`PIVOT`和`UNPIVOT`只在 Oracle 与 SQL Server 起作用。

在 MySQL 中行转列可以通过子查询与`case when`判断或者IF聚合函数实现，列转行可以通过UNION操作符实现。

**[884]** 数据库连接数设置大小，由PostgreSQL提供，`连接数 = ((核心数 * 2) + 有效磁盘数)`。

**[883]** 注意，在sql语句的 or 条件后面补充的 and 条件，可能会执行出预期之外的的结果！！！

**[882]** COUNT 的不同方式的比较。

COUNT(主键ID) InnoDB遍历全表，把每一行的主键值都取出来返回给MySQL的Server层，因为主键不可能为NULL，Server层直接按行累加最后返回累计值给客户端。

COUNT(1) 遍历全表但不取值，Server层对返回的每一行放个数字"1"进去，按行累加。COUNT(1)比COUNT(主键)快，因为不需要取值，减少了数据传输。

COUNT(字段) 遍历全表，一行行从记录中读出字段值给Server层，Server层判断值不为NULL了再累加。

COUNT(*) MySQL专门做了优化，会找到表中最小的索引树，InnoDB普通索引树比主键索引小很多，对于COUNT(*)遍历哪个树是一样的，count(*)时MySQL不取记录值，count(*)也肯定不为NULL，Server层中直接按行累加。
       
所以这个版本COUNT的从低到高分别为：COUNT(字段) < COUNT(主键) < COUNT(1) ≈ COUNT(*)

**[881]** 椭圆曲线ECC，目前最难加密，但是速度慢。

**[880]** Lombok 的用法

@Singular 注解，不需要new 列表

private List<String> favorites;

@Wither 链式写法，比@Builder，with() 返回的对象并不是原来的对象，而是一个新对象

Accessors，访问器模式，是给一个普通的Bean增加一个便捷的访问器，包括读和写。

它有两种工作模式，fluent和chain，使用属性同名字符串代替了getter和setter。

**[879]** 反射调用 default 方法
``` java
      Hello object = (Hello) Proxy.newProxyInstance(Thread.currentThread().getContextClassLoader(),new Class[]{Hello.class}, (Object proxy, Method method, Object[] arguments) -> null);
      Method method = Hello.class.getMethod("hello");
      Class<?> declaringClass = method.getDeclaringClass();

      Constructor<MethodHandles.Lookup> constructor = MethodHandles.Lookup.class.getDeclaredConstructor(Class.class, int.class);
      constructor.setAccessible(true);
      MethodHandles.Lookup lookup = constructor.newInstance(declaringClass, -1);
      Object result = lookup
              .unreflectSpecial(method, declaringClass)
              .bindTo(object)
              .invokeWithArguments(args);

```
**[878]** 迪米特法则（Law of Demeter）又叫作最少知识原则（Least Knowledge Principle 简写LKP）

就是说一个对象应当对其他对象有尽可能少的了解,不和陌生人说话。英文简写为: LoD.
          
迪米特法则的初衷在于降低类之间的耦合。由于每个类尽量减少对其他类的依赖，因此，很容易使得系统的功能模块功能独立，相互之间不存在（或很少有）依赖关系。

**[877]** ASM字节码增强技术主要是用来反射的时候提升性能的，如果单纯用jdk的反射调用，性能是非常低下的，而使用字节码增强技术后反射调用的时间已经基本可以与直接调用相当了。

**[876]** CPU标高的一般处理步骤
- top查找出哪个进程消耗的cpu高
- top –H –p查找出哪个线程消耗的cpu高
- 记录消耗cpu最高的几个线程
- printf %x 进行pid的进制转换
- jstack记录进程的堆栈信息
- 找出消耗cpu最高的线程信息

**[875]** 内存标高（OOM）一般处理步骤
- jstat命令查看FGC发生的次数和消耗的时间，次数越多，耗时越长说明存在问题；
- 连续查看jmap –heap 查看老生代的占用情况，变化越大说明程序存在问题；
- 使用连续的jmap –histo:live 命令导出文件，比对加载对象的差异，差异部分一般是发生问题的地方。

**[874]** 捕捉线程或者线程组对象的异常

方法一，设置UncaughtExceptionHandler成员变量
```
Thread t = new Thread(r);
t.setUncaughtExceptionHandler(
    (t1, e) -> LOGGER.error(t1 + " throws exception: " + e)
);
return t;
```
方法二，重写线程池的`afterExecute`方法。

**[873]** python是动态语言，一个变量所指向对象的类型在运行时才确定，编译器做不了任何预测，也就无从优化。

python是解释执行，但是不支持JIT(just in time compiler)。

python中一切都是对象，每个对象都需要维护引用计数，增加了额外的工作。

python GIL，多线程并不能真正的并发。

垃圾回收，这个可能是所有具有垃圾回收的编程语言的通病。python采用标记和分代的垃圾回收策略，每次垃圾回收的时候都会中断正在执行的程序，造成所谓的顿卡。
       
       
**[872]** 出现org.apache.http.NoHttpResponseException: The target server failed to respond异常报警
查看http服务端的nginx配置，发现设置了keepAlive 32s，也就是说当连接空闲超过32s时，服务端就会主动发起FIN请求，进入tcp关闭的四次挥手流程。由于httpclient没有主动关闭连接，导致连接处于半关闭状态，而连接还存在于连接池中，所以当下次再被取出来用时就会收到服务端的RST。

1、通过PoolingHttpClientConnectionManager.setValidateAfterInactivity(evictIdle);设置连接空闲多久以后需要进行validate检查
2、通过HttpClientBuilder.evictIdleConnections(evictIdle, TimeUnit.MILLISECONDS)设置连接空闲多久后将被清理出连接池。
因此只要我们配置上这两个参数，保证它小于服务端保持连接的时间就可以了。