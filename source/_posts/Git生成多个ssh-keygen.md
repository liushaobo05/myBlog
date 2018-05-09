---
title: Git生成多个ssh-keygen
date: 2018-05-03 22:28:09
tags:
---

> 作为新时代的开发者，大家一般会拥有自己的Github工程。同时公司又提供单独gitlab服务器，所以经常会遇到需要在同一设备下配置多个ssh key的情况，下述会阐述如何进行设置操作：

##### 第一步：生成指定名称的秘钥
```
$ ssh-keygen
```

##### 第二步：配置config文件
在~/.ssh文件夹下创建config文件,添加以下内容
```
Host 192.168.0.45
IdentityFile ~/.ssh/id_rsa.idss
```
字段 | 说明
---|---
Host | 远程主机地址
IdentityFile | 私钥的文件路径及文件名称
User | 用户
Port | 远程主机上连接的端口号
HostName | 要登录的真实主机名。数字IP地址也是允许的

##### 第三步：复制新生成的公钥到服务器
复制公钥到github或相关gitlab,ssh key配置项中

##### 第四步：修改相关用户配置
```
$ git config user.name "your name"
$ git config user.email "your email"
```