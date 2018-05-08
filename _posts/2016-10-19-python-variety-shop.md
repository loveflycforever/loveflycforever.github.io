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

**[009]** 用 fromkeys 方法将列表转换成字典
``` python
keys = [1, 2, 3]
value = []
d = dict.fromkeys(keys, value)
```

**[010]** 用 collections 模块中的 defaultdict 初始化字典对象
```
>>> from collections import defaultdict
>>> dd = defaultdict(list)
>>> dd
defaultdict(<type 'list'>, {})
```
defaultdict 类的初始化函数接受一个类型作为参数，当所访问的键不存在的时候，可以实例化一个值作为默认值。
```
>>> from collections import defaultdict
>>> def zero(): return 0
>>> dd = defaultdict(zero) # dd = defaultdict(lambda: 0) 
>>> dd
defaultdict(<function zero at 0xb7ed2684>, {})
>>> dd['foo']
0
>>> dd
defaultdict(<function zero at 0xb7ed2684>, {'foo': 0})
```
除了接受类型名称作为初始化函数的参数之外，还可以使用任何不带参数的可调用函数，到时该函数的返回结果作为默认值。

默认值只有在通过`dict[key]`或者`dict.__getitem__(key)`访问的时候才有效。因为从2.5版本开始，如果派生自dict的子类定义了`__missing__()`方法，当访问不存在的键时，`dict[key]`会调用`__missing__()`方法取得默认值。


**[011]** 用 setdefault 为字典中不存在的 key 设置缺省值
- 如果 key 存在于字典中，那么直接返回对应的值，等效于 get 方法
- 如果 key 不存在字典中，则会用 setdefault 中的第二个参数作为该 key 的值，再返回该值。

**[012]** 可变数据类型作为函数定义中的默认参数时，当编写函数时，这个可变数据类型被实例化为函数定义的一部分。
```
def fn(var1, var2=[]): 
    var2.append(var1) 
    print var2 
fn(3) # 目标输出 [3] 实际输出 [3]  
fn(4) # 目标输出 [4] 实际输出 [3, 4]  
fn(5) # 目标输出 [5] 实际输出 [3, 4, 5]  
```
当函数运行时，可变数据类型并不是每次都被实例化。
```
def fn(var1, var2=None): 
    if not var2: 
        var2 = [] 
    var2.append(var1) 
```
注意，对于不可变数据类型，比如元组、字符串、整型，是不需要考虑这种情况的。

**[013]** 可变数据类型：列表list和字典dict；不可变数据类型：整型int、浮点型float、字符串型string和元组tuple。

**[014]** 扫描字符串是否包含指定的字符
```
str1 = '12345678'
str2 = '456'
print len(str1 and str2)
```
