---
layout: post
title: 单机zookeeper的安装
date: 2016-9-24 22:37:43
excerpt: "单机zookeeper的安装"
categories: [c/cpp]
tags: [zookeeper]
---

本文主要介绍下单机环境下zookeeper的安装，分为两个步骤：java环境搭建和zookeeper环境搭建，方便起见，放了一份在[百度云](http://pan.baidu.com/s/1kVPjbRx)上。

<!--more-->

### 1. 搭建java环境

从[oracle官网](http://www.oracle.com/technetwork/java/javase/downloads/index.html)下载jdk.x.tar.gz文件并解压，我选择的版本是jdk1.8.0_102。

配置环境变量，修改.bashrc

```
JAVA_HOME=刚才的解压路径
PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib:$JAVA_HOME/jre/lib:$JAVA_HOME/jre/lib/rt.jar:$CLASSPATH
```

source ~/.bashrc生效

验证是否安装成功：

```
public class test {
    public static void main(String args[]) {
        System.out.println("A new jdk test!");
    }
}
```
将上述代码存为test.java，然后运行javac test.java会在当前目录下生成test.class文件，再执行java test如果能够输出`A new jdk test!`则表明java安装成功。

### 2. 搭建zookeeper环境

从[官网镜像](http://mirror.bjtu.edu.cn/apache/zookeeper/stable/)
下载zookeeper-x.tar.gz文件并解压，为了描述方便，假设解压后的目录路径为`/home/yingshin/zookeeper`，我这里选择的是zookeeper-3.4.8。

#### 2.1 创建数据及日志目录

```
mkdir -p /home/yingshin/zookeeper/data
mkdir -p /home/yingshin/zookeeper/log
```

#### 2.2 配置zookeeper

```
cp /home/yingshin/zookeeper/conf/zoo_sample.cfg /home/yingshin/zookeeper/conf/zoo.cfg
vim /home/yingshin/zookeeper/conf/zoo.cfg
```

修改如下配置:

```
tickTime=2000 #zookeeper 服务器之间或者服务器与客户端之间心跳时间，每隔该时间发送一个心跳，单位为ms
initLimit=10  #投票选举新leader的初始化时间
syncLimit=5  #leader与follower心跳检测最大容忍时间，响应超过tickTime*syncLimit，认为leader 丢失该follower
clientPort=2181 #客户端连接服务端的端口，zookeepr会监听这个端口，接收客户端的访问请求
dataDir=/home/yingshin/zookeeper/data #数据目录
dataLogDir=/home/yingshin/zookeeper/log #日志目录
```

#### 2.3 配置日志

`vim /home/yingshin/zookeeper/bin/zkEnv.sh` 修改为：

```
if [ "x${ZOO_LOG_DIR}" = "x" ]  
then  
   ZOO_LOG_DIR="/home/yingshin/zookeeper/log"  
fi  

if [ "x${ZOO_LOG4J_PROP}" = "x" ]  
then  
   ZOO_LOG4J_PROP="INFO,ROLLINGFILE"  
fi
```

#### 2.4 配置环境变量，修改.bashrc

```
ZOOKEEPER_HOME=/home/yingshin/zookeeper
PATH=$ZOOKEEPER_HOME/bin:$PATH
```

source .bashrc生效

#### 2.5 启停zookeeper服务

`zkServer.sh`负责服务的启停，启停时可以指定启动的cfg配置，默认为zoo.cfg


```
#指定cfg启动zookeeper
$ sh zkServer.sh start ../conf/zoo.cfg
ZooKeeper JMX enabled by default
Using config: ../conf/zoo.cfg
Starting zookeeper ... STARTED

#指定cfg查看zookeeper服务状态
$ sh zkServer.sh status ../conf/zoo.cfg 
ZooKeeper JMX enabled by default
Using config: ../conf/zoo.cfg
Mode: standalone

#指定cfg停止zookeeper服务
$ sh zkServer.sh stop./conf/zoo.cfg
ZooKeeper JMX enabled by default
Using config: ../conf/zoo.cfg
Stopping zookeeper ... STOPPED
```

启动时如果看到以上输出，则表示zookeeper服务已经正常启动，可以通过`netstat -anp | grep 2181`验证zookeeper是否已经监听配置端口。


### 3. 参考资料：

1. [分布式服务框架 Zookeeper -- 管理分布式环境中的数据](http://www.ibm.com/developerworks/cn/opensource/os-cn-zookeeper/)

