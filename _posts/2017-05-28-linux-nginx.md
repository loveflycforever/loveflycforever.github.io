---
layout: post
title:  "配置nginx以实现负载均衡"
date:   2017-05-28
excerpt: "Linux nginx。"
tags: [Linux, nginx]
feature: 
comments: true
---
> `uname -a`

基于

> Linux debian 4.9.0-4amd64

1. 下载的软件存放位置`/var/cache/apt/archives`
2. 安装后软件位置`/usr/share`
3. 可执行文件位置 `/usr/sbin`
4. 配置文件位置`/etc`
5. lib文件位置`/usr/lib`

### nginx 安装

`~# apt-get update`

`~# apt-get install nginx`

### nginx 启动

`~# nginx`

或者指定配置文件

`~# nginx -c /etc/nginx/nginx.conf`

获取所有正在运行的nginx进程的列表

`~# ps -ax|grep nginx`

可以看到目前有一个nginx: master process 和一个nginx: worker process 进程。

引述 nginx 文档

> nginx has one master process and several worker processes. The main purpose of the master process is to read and evaluate configuration, and maintain worker processes. Worker processes do actual processing of requests. nginx employs event-based model and OS-dependent mechanisms to efficiently distribute requests among worker processes. The number of worker processes is defined in the configuration file and may be fixed for a given configuration or automatically adjusted to the number of available CPU cores (see worker_processes).

nginx有一个主进程和几个工作进程。主进程的主要目的是读取和评估配置，并维护工作进程。工作进程会实际处理请求。nginx使用基于事件的模型和依赖于操作系统的机制来高效地在工作进程间分配请求。工作进程的数量在配置文件中定义，并且可以针对给定配置进行修复或自动调整为可用CPU核心的数量。

### nginx 配置文件

`~# vim /etc/nginx/nginx.conf`

nginx由配置文件中指定的指令控制的模块组成。指令分为简单指令和块指令。简单指令由用空格分隔的名称和参数组成，并以分号( ; )结尾。 块指令与简单指令具有相同的结构，但不是以分号结尾，而是以大括号( { 和 } )包围的一组附加指令结束。如果块指令可以在大括号内包含其他指令，则它被称为上下文(例如：events, http, server, 和 location)。

放置在配置文件中的并且在所有上下文之外的指令被认为是处于 main 上下文中。 events 和 http 指令处于在 main 上下文中，server 处于 http 上下文中, location 处于 server 上下文中。

＃号之后的内容是注释。

### nginx 命令行

- nginx -s stop 

快速关机

- nginx -s quit

优雅关机，等待工作进程完成当前请求才停止nginx进程。

- nginx -s reload 

重新加载配置文件

主进程收到重新加载配置的信号后，会检查新配置文件的语法有效性，并尝试应用其中提供的配置。如果成功，主进程启动新的工作进程并将消息发送给旧工作进程，请求它们关闭。否则，主进程将回滚更改并继续使用旧配置。旧工作进程接收关闭的命令，停止接受新的连接并继续服务当前的请求，直到所有这样的请求服务完成。在此之后，旧工作进程退出。

- nginx -s reopen

重新打开日志文件

### 命令行参数
|参数|使用|
|:---:|:---|
|-? 或 -h | 打印命令行参数的帮助信息。
|-c file | 使用指定替代的配置文件而不是默认文件。
|-g directives | 设置全局配置指令，例如，`nginx -g "pid /var/run/nginx.pid; worker_processes 'sysctl -n hw.ncpu';"`
|-p prefix | 设置 nginx 路径前缀，即保留服务器文件的目录(默认值是 /usr/local/nginx)。
|-q | 在配置测试期间抑制非错误消息。
|-s signal | 发送一个信号给主进程。 参数信号可以是以下之一：
||- `stop` 快速的关闭
||- `quit` 优雅的关闭
||- `reload` 重新加载配置，使用新配置启动新的工作进程，优雅地关闭旧的工作进程。
||- `reopen` 重新打开日志文件
|-t | 测试配置文件：nginx检查配置的正确语法，然后尝试打开配置中引用的文件。
|-T | 与-t相同，但还会将配置文件转储到标准输出（1.9.2）。
|-v | 打印nginx版本。
|-V | 打印nginx版本，编译器版本和配置参数。


