---
layout: post
title:  "杂货店"
date:   2014-08-17
excerpt: "来来往往，想到就记录一点。"
project: true
tags: [杂货]
feature: https://upload.wikimedia.org/wikipedia/commons/4/48/Window_shopping_in_Cowbridge_-_geograph.org.uk_-_1770681.jpg
comments: true
---
**[999]** Windows 系统下批量修改文件名

```
ren *.java *.txt
```

**[998]** Appsrver 2.6.0 运行初始异常

C:\Windows\System32\drivers\etc 目录下 hosts 文件，去除 127.0.0.1 localhost 前的 # 符号。
    
**[997]** 解决 MySQL Error 1366

办法1：停止 MySQL 服务，修改 my.ini 文件，修改其中的 [mysql] default-character-set=gbk 和 [mysqld] default-character-set=utf8

办法2：打开 MySQL 命令行客户端，在输入的每一行命令前输入 set names gbk
    
**[996]** 绝对路径问题
    
```
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

**[995]** 复制文本至粘贴板
    
```
<script>
function copywenben() {
    var content = document.getElementById("content").innerText;
    window.clipboardData.setData("Text", content);
}
</script>
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

**[991]** document.write

```
<script>
    document.write("<ul><li><input type="checkbox">a</li></ul>");
</script>
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

**[988]** 使用 Iterator.remove 方法更新集合中的内容，不需要创建额外的集合，可以节省空间，同时避免并发异常

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

**[985]** 两个准则

- 如果两个对象相等，那么 hashCode 必须相等。
- 如果两个对象有相同的 hashCode 那么它们可能相等也可能不相等。

**[984]** 语法糖

语法糖（Syntactic sugar），也译为糖衣语法，是由英国计算机科学家彼得·约翰·兰达（Peter J. Landin）发明的一个术语，指计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用。

通常来说使用语法糖能够增加程序的可读性，从而减少程序代码出错的机会。

**[983]** 鸭子类型

一只鸟走起来像鸭子、游起泳来像鸭子、叫起来也像鸭子，那它就可以被当做鸭子。也就是说，它不关注对象的类型，而是关注对象具有的行为（方法）。

**[982]** Java ObjectIO
- transient 关键字，透明化，在写向硬盘时不予考虑
- Serializable 序列化
- Externalizable 可控制的序列化

**[981]** 多态
- 要有继承（extends）
- 要有重写（override）
- 父类引用要指向子类对象（A a = new B()）

**[980]** (obj instanceof String) 类型判断，运算符

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

**[971]** HTML 标签`<a>`和 CSS 属性 display:block 适合在一起使用。

**[970]** HTML 页面加载完才会执行 onload 事件，适用于延迟加载。

**[969]** HTML 标签`<frameset>`与 HTML 标签`<body>`不能一起使用

因为`<frameset>`是一种框架结构，会将整个页面占据掉，所以不要用`<body>`进行布局，并确保 DOCTYPE 被设置为 "Frameset DTD"。

**[968]** CSS 属性 display 的值的说明

- block（块对象的默认值）将对象强制作为块对象呈递，为对象之后添加新行。

- inline（内联对象的默认值）将对象强制作为内联对象呈递，从对象中删除行。

- inline-block（将对象呈递为内联对象，但是对象的内容作为块对象呈递）旁边的内联对象会被呈递在同一行内。

- none（隐藏对象）与 visibility 属性的hidden值不同，其不为被隐藏的对象保留其物理空间。

**[967]** CSS 属性 required 适用于以下类型的`<input>`标签

text、search、url、telephone、email、password、date pickers、number、checkbox、radio和file。

**[966]** 在 HTML `<canvas>`标签中定义 width、height 跟在 CSS style 中定义 width、height 是不同的

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
```css
#main {
    width:100px!important;
    width:10px;
    border:1px solid #000;
}
```
!important是CSS1就定义的语法，是用于提高指定样式规则的应用优先级。

IE一直都不支持这个语法，而其他的浏览器都支持，可以利用它来分别给火狐浏览器和IE浏览器样式不同的定义，只要在样式后面加上!important。

**[963]** MyEclipse 的 Servers 视图出现 Could not create the view: An unexpected exception was thrown.警告的解决办法

到工作空间下面的workspaces\.metadata\.plugins\org.eclipse.core.runtime\.settings目录下面找到了名称为com.genuitec.eclipse.ast.deploy.prefs文件，将此文件删除，重启MyEclipse10问题解决。

**[962]** 配置ADT环境变量

打开PATH，添加变量值：C:\android-sdk-windows\tools

**[961]** Android命令
- android 弹出Android SDK and AVD Manager
- android list avds 列出所有创建的Android模拟器
- android list targets 列出所有的SDK可用版本
- adb devices 列出所有可装载的设备
- adb shell 进入设备的根目录
- adb installs <AVD名称> <APK文件所在位置>
- emulator -avd <AVD名称>
- mksdcard 256M <d:/sdcard.img> 将在D盘下生成256M的sdcard
- adb pull <remote> <local>/adb push <local> <remote> 取出/存放文件
- android create avd --name <AVD名称> --target <SDK版本>
