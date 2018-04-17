---
layout: post
title:  "Python 杂货店"
date:   2016-10-19
excerpt: "记录一点。"
project: true
tags: [Python, 杂货]
feature: https://pypi.python.org/static/images/python-logo.png
comments: true
---
**[001]** Python 没有类似 public / protected / private 的访问控制，如果要表示“私有”，习惯是加双下划线前缀。

**[002]** 抽象类（方法），让方法抛出异常 NotImplementedError，在类（方法）名字上标志它是抽象的（Abstract），体现它是一个接口（抽象类）。
``` python
class AbstractClass:  
    def abstractMethod(self): 
    	raise NotImplementedError  
```
不过依然可以实例化 AbstractClass。

**[003]** 定义1个元素的元组（tuple）
``` python
t = (1,) 
```

**[003]** 应用 PIL 类库
- `pip install PIL --allow-external PIL --allow-unverified PIL`，此方法无效。
- `pip install Pillow`，Pillow由PIL而来，所以该导入该库并使用`import PIL`。

**[004]** pip 的安装
1. 下载`get-pip.py`
> https://bootstrap.pypa.io/get-pip.py
2. 执行`python get-pip.py`