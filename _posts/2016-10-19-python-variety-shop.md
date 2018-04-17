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

**[004]** 应用 PIL 类库
- `pip install PIL --allow-external PIL --allow-unverified PIL`，此方法无效。
- `pip install Pillow`，Pillow由PIL而来，所以该导入该库并使用`import PIL`。

**[005]** pip 的安装
1. 下载`get-pip.py`
> https://bootstrap.pypa.io/get-pip.py
2. 执行`python get-pip.py`

**[006]** 用 in 关键字检查字典中 key 是否存在，相较于 has_key 方法，in 同时兼容两个版本的代码。
``` python
d = {'name': 'python'}
if 'name' in d:
    pass

```

**[007]** 使用 items 方法迭代字典中的元素，返回的是(key, value)组成的列表对象，内存瞬间会扩大两倍，因为列表对象会一次性把所有元素加载到内存。

更好的方式是使用 iteritems 方法，返回的是迭代器对象，Python3 中，只有 items 方法，等价于 Python2 中 iteritems 方法。
``` python
d = {'name': 'python'}
for k, v in d.iteritems():
    print(k, v)
```

**[008]** 使用字典推导式构建字典对象
``` python
numbers = [1, 2, 3]
d = {number: number * 9 for number in numbers}
```

**[]** 用 fromkeys 方法将列表转换成字典
``` python
keys = [1, 2, 3]
value = []
d = dict.fromkeys(keys, value)
```