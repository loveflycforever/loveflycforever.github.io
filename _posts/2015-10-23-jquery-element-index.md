---
layout: post
title:  "JQuery 获取元素索引值"
date:   2015-10-23
excerpt: "一种错误，一种正确。"
tags: [JQuery]
comments: true
---
例：
```
<html>
<body>
<div id="text">
	<div>
		<a><span>标题一</span></a>
		<p>文段一</p>
	</div>
	<div>
		<a><span>标题二</span></a>
		<p>文段二</p>
	</div>
	<div>
		<a><span>标题三</span></a>
		<p>文段三</p>
	</div>
</div>
</body>
</html>
```

错误写法：
``` javascript
$("#text div").click(function() {
	var index = $(this).index();
	alert(index);
});
```
以上，是获取不了正确的索引值的，返回值一直为0。

正确写法：
``` javascript
$("#text div").click(function() {
	var index = $("#text div").index(this);
	alert(index);
});
```

原因：对于elsement.index()产生误解，以为$(this)就是当前元素，而index()就可以获得它在同辈元素中的索引号，然而，必须先指定这个元素原先所在的集合，才可以获得这个元素的索引。

