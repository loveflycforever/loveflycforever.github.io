---
layout: post
title:  "Java 单例"
date:   2016-08-09
excerpt: "茴的四种写法"
tags: [Java, 设计模式, 单例, singleton]
comments: true
---
- 饿汉

``` java
private Singleton() {}

private static Singleton instance = new Singleton();

private static Singleton getInstance() {
    return instance;
}
```

- 懒汉

``` java
private Singleton() {}

private static Singleton instance;

public static synchronized Singleton getInstance() {
    if(instance == null) {
        return instance = new Singleton();
    } else {
        return instance;
    }
}   
```

- 双检锁

``` java
private Singleton() {}

private volatile static Singleton instance;

public static Singleton getInstance() {
    if(instance == null) {
        synchronized (Singleton.class) {
            if(instance == null) {
                instance = new Singleton();
            }
        }
    }
    return instance;
}
```

- 内部类

``` java
private Singleton(){}

private static class SingletonHolder {
    private static Singleton instance = new Singleton();
}

private static Singleton getInstance() {
    return SingletonHolder.instance;
}
```
