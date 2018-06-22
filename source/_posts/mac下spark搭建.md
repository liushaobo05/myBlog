---
title: mac下spark搭建
date: 2018-06-22 14:15:25
tags:
  - spark
---
#### spark是什么？
spark是一个大数据集群处理框架，它比hadoop处理的效率要高很多。

#### 安装
- java 环境安装
在安装之前需要安装java运行环境，要求java 8 环境。在官网下载jdk(http://www.oracle.com/technetwork/java/javase/downloads/index.html)

- spark环境安装
官网(http://spark.apache.org/downloads.html)下载spark，最新版本(spark-2.3.1-bin-hadoop2.7.tgz)
解压后，设置该文件夹下的bin到path中，方便快速启动。


- 配置路径与日志级别
```
# 进入配置目录
cd /opt/spark-2.3.1-bin-hadoop2.7/conf

# 基于模板创建日志配置文件
cp log4j.properties.template log4j.properties

# 使用vim或gedit编辑文件log4j.properties
# 修改log4j.rootCategory为WARN, console，可避免测试中输出太多信息
log4j.rootCategory=WARN, console
```

#### 命令行交互终端
Spark-Shell是Spark自带的一个Scala交互Shell，可以以脚本方式进行交互式执行
pyspark类似spark-shell，是一个Python的交互Shell。

#### 服务启动
- 启动主节点
```
# 进入到spark目录
cd /opt/spark-2.3.1-bin-hadoop2.7

# 启动主节点
./sbin/start-master.sh
```
没有报错的话表示master已经启动成功，master默认可以通过web访问http://localhost:8080

- 启动从节点
```
./sbin/start-slave.sh spark://2e2a2f2awwee:7077
```
jps命令查看启动的服务，应该会列出Master和Slave

- 测试
```
MASTER=spark://2e2a2f2a21ff:7077 pyspark  #执行需要等待一小会
```
- 停止服务
```
./sbin/stop-all.sh
jps
```
