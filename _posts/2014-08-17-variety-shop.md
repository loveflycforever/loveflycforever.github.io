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
[999] Windows 系统下批量修改文件名

```
ren *.java *.txt
```

[998] Appsrver 2.6.0 运行初始异常

系统盘:\Windows\System32\drivers\etc 目录下 hosts 文件，去除 127.0.0.1 localhost 前的 # 符号。
    
[997] 解决 MySQL Error 1366

办法1：停止 MySQL 服务，修改 my.ini 文件，修改其中的 [mysql] default-character-set=gbk 和 [mysqld] default-character-set=utf8

办法2：打开 MySQL 命令行客户端，在输入的每一行命令前输入 set names gbk
    
[996] 绝对路径问题
    
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

[995] 复制文本至粘贴板
    
```
<script>
function copywenben() {
    var content = document.getElementById("content").innerText;
    window.clipboardData.setData("Text", content);
}
</script>
```

[994] 更改 MyEclipse 中 jsp 的默认打开方式

window---preferences---general---editor---file associations---找到 jsp 类型，选择默认打开方式为 jsp editor
    
[993] 更改 MyEclipse 中 Java 的编译版本

`Syntax error, parameterized types are only available if source level is 1.5`

Window---preferences---Java---compiler---更改 Compiler compliance level

右击项目，修改properties，找到 java compiler 修改 compiler compliance level

[992] PHP 页面抓取

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

[991] document.write

```
<script>
    document.write("<ul><li><input type="checkbox">a</li></ul>");
</script>
```

[990] 避免过度使用 null 值

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

[989] 强调某个异常不重要的一个简便途径就是将此信息作为异常的变量名

``` java
try {
    doSomethingHere();
} catch (NullPointerException unimportant) {}
```

[988] 使用 Iterator.remove 方法更新集合中的内容，不需要创建额外的集合，可以节省空间，同时避免并发异常

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
        strIterator.add("new");
        /*
        * 或者使用
        * strIterator.set("new");
        * */
    }
}
```

``` java
// Java 8 Method
oneStrList.removeIf(String::isEmpty);
```

[987] Java8 可以将一个 collection 转换为 stream，并且根据条件过滤 stream

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

[986] 用 CopyOnWriteArrayList、CopyOnWriteSet、ConcurrentHashMap 等特殊的集合处理，缺点是每次修改都产生一个新的集合。

[985] 两个准则

- 如果两个对象相等，那么 hashCode 必须相等。
- 如果两个对象有相同的 hashCode 那么它们可能相等也可能不相等。

[984] 语法糖

语法糖（Syntactic sugar），也译为糖衣语法，是由英国计算机科学家彼得·约翰·兰达（Peter J. Landin）发明的一个术语，指计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用。

通常来说使用语法糖能够增加程序的可读性，从而减少程序代码出错的机会。

[983] 鸭子类型

一只鸟走起来像鸭子、游起泳来像鸭子、叫起来也像鸭子，那它就可以被当做鸭子。也就是说，它不关注对象的类型，而是关注对象具有的行为（方法）。

