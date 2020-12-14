---
layout: post
title:  "常用 Linux 命令"
subtitle: ""
tags: [Linux]
bigimg: https://www.openssh.com/images/openssh.gif
comments: true
---

显示文件或目录信息
> ls

当前目录下创建一个空目录
> mkdir

要求目录为空
> rmdir

生成一个空文件或更改文件的时间
> touch

复制文件或目录
> cp

移动文件或目录、文件或目录改名
> mv

删除文件或目录
> rm

建立链接文件
> ln

查找文件
> find

查看文件类型或文件属性信息
> file/stat

查看文本文件内容
> cat

可以分页看
> more

不仅可以分页，还可以方便地搜索，回翻等操作
> less

查看文件的尾部的10行
> tail -10

查看文件的头部20行
> head -20

把内容重定向到指定的文件中 ，有则打开，无则创建
> echo 


    管道命令 | ：将前面的结果给后面的命令，例如：ls -la | wc ，将ls的结果加油wc命令来统计字数

    重定向 > 是覆盖模式，>> 是追加模式，例如：echo "Java3y,zhen de hen xihuan ni" > qingshu.txt 把左边的输出放到右边的文件里去

