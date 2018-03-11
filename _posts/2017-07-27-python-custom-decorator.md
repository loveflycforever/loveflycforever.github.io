---
layout: post
title:  "Python 装饰器"
date:   2017-07-27
excerpt: "decorator 可以修改被装饰的对象。"
tags: [Python, 装饰器, decorator]
feature: https://www.python.org/static/img/python-logo-large.png?1414305901
comments: true
---
装饰器，是一个函数，接受一个函数(或者类)作为参数，返回值也是也是一个函数(或者类)。

设计模式，装饰器模式，动态地为某个对象增加额外的责任。
1. 修改被装饰对象的属性或者行为。
2. 处理被函数对象执行的上下文，比如设置环境变量，加log之类。
3. 处理重复的逻辑。
4. 框架代码，如flask， bottle等等。本质上也避免了重复代码。

【例】函数装饰器，@customDecorator

``` python
import functools


def customDecorator(func):
    # @functools.wraps(func) 用于获取被装饰函数的名称
    @functools.wraps(func)
    def wrapped(*args, **kwargs):
        import time
        begin = time.time()
        try:
            return func(*args, **kwargs)
        finally:
            print('func %s spend %s' % (func.__name__, time.time() - begin))

    return wrapped

```

【例】带参数的函数装饰器，@customDecorator2(sys.stdout)

``` python
def customDecorator2(stream):
    def inner_dec(func):
        def wrapped(*args, **kwargs):
            import time
            begin = time.time()
            try:
                return func(*args, **kwargs)
            finally:
                stream.write('func %s spend %s \n' % (func.__name__, time.time() - begin))
        return wrapped
    return inner_dec
```

【例】类注释器（例，修改类的__str__方法），@classDecorator

``` python
def classDecorator(clazz):
    clazz.__str__ = lambda s: "classDecorator"
    return clazz
```
