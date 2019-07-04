---
layout: post
title:  "hadoop install"
subtitle: "hadoop install"
tags: [hadoop install]
comments: true
---
配置环境变量 Java 主目录
> vim ~/.bashrc
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
> source ~/.bashrc

****************************************************************************************

运行 hadoop 
hadoop 目录下 ./bin/hadoop

************************************************************************************

运行示例程序

hadoop目录下
./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples.xxxx.jar 

**********************************************************************************

默认安装单机模式，可切换成伪分布式的部署方式

目录下 ./etc/hadoop/core-site.xml 的 configuration 节点
<configuration>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>file:/srv/local_hadoop/tmp</value>
        <description>Local Hadoop temporary directories.</description>
    </property>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>

目录下 ./etc/hadoop/hdfs-site.xml 的 configuration 节点
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:/srv/local_hadoop/tmp/dfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/srv/local_hadoop/tmp/dfs/data</value>
    </property>
</configuration>

官方配置木有配置目录节点信息，默认使用的临时目录，存在被系统清理的风险。

然后格式化 namenode
> ./bin/hdfs namenode -format

第一次启动一定要格式化管理节点

启动 > ./sbin/start-dfs.sh

> jps
4195 Jps
1156 DataNode
1029 NameNode
1320 SecondaryNameNode

访问 ip:50070 查看


**********************************************************************
创建用户目录
./bin/hdfs dfs -mkdir -p /user/<username>
如果是root用户就是/user/root

创建输入目录
./bin/hdfs dfs -mkdir -p input
-p 参数强制生成所有目录
查看所有目录
./bin/hdfs dfs -ls -R /
可以看到在user下的root文件夹下的input目录

将etc下的所有xml文件作为测试数据放入输入目录中
./bin/hdfs dfs -put ./etc/hadoop/*.xml input
分布式运行 MapReduce 作业的方式跟单机模式相同，区别在于伪分布式读取的是HDFS中的文件
./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-ex amples-2.9.2.jar grep input output 'y'
查看这个分组任务的结果
./bin/hdfs dfs -cat output/*
Hadoop 运行程序时，输出目录不能存在，已存在需删除
./bin/hdfs dfs -rm -r output 

将数据获取到本地，hadoop安装目录下
./bin/hdfs dfs -get output ./local_from_output 

停止hadoop服务
./sbin/stop-dfs.sh

*********************************************************************




hadoop 环境配置文件
./hadoop/etc/hadoop/hadoop-env.sh


*********************************************************************

YARN 是从 MapReduce 中分离出来的，负责资源管理与任务调度。

./sbin/start-dfs.sh 仅仅是启动了 MapReduce 环境，启动 YARN 需要做一下配置

目录下 ./etc/hadoop/yarn-site.xml 的 configuration 节点
<configuration>
    <property>
    	<name>yarn.nodemanager.aux-services</name>
    	<value>mapreduce_shuffle</value>
    </property>
</configuration>

复制
目录下 cp ./etc/hadoop/mapred-site.xml.template ./etc/hadoop/mapred-site.xml

目录下./etc/hadoop/mapred-site.xml  的 configuration 节点
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>

启动 YARN （注意需要先执行过 ./sbin/start-dfs.sh）
./sbin/start-yarn.sh  

> jps
4195 Jps
1156 DataNode
1029 NameNode
1320 SecondaryNameNode
3785 ResourceManager
4076 NodeManager

出现 ResourceManager 和 NodeManager

访问 ip:8088 查看


历史服务器，用于在Web中查看任务运行情况
./sbin/mr-jobhistory-daemon.sh start historyserver


停止服务
./sbin/stop-yarn.sh

./sbin/mr-jobhistory-daemon.sh stop historyserver

注意：
不启动 YARN 需重命名 mapred-site.xml，否则在该配置文件存在，而未开启 YARN 的情况下，运行程序会提示报错

*******************************************************

sshd的设置允许root用户用密码远程登录

vim /etc/ssh/sshd_config
添加或者修改为
PermitRootLogin yes


****************************************************************

1. hadoop fs
2. hadoop dfs
3. hdfs dfs

hadoop fs适用于任何不同的文件系统，比如本地文件系统和HDFS文件系统
hadoop dfs只能适用于HDFS文件系统
hdfs dfs 跟hadoop dfs的命令作用一样，也只能适用于HDFS文件系统