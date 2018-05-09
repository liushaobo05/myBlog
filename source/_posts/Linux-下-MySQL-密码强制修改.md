---
title: Linux 下 MySQL 密码强制修改
date: 2018-05-09 15:08:08
categories: mysql
tags:
  - mysql
---
#### 一、停掉mysql服务
```
$service mysqld stop
```

#### 二、启动mysql，禁止权限验证
```
#在mysql安装目录的bin目录下执行(&后台执行)
$/usr/local/mysql/bin/mysqld_safe --skip-grant-tables &
```

#### 三、本地登录，更改密码
```
#本地 localhost登录无需密码
$mysql -u root
#切换数据库
$mysql>use mysql;
#更改密码
$mysql>update user set password=password("newpasswd") where user="root";
#刷新权限
$mysql>flush privileges;
#退出
$mysql>\q
```

#### 四、启动mysql

- 停掉安全模式启动的服务
```
#查询mysql是否启动
$ps aux | grep "mysql"
#如果发现 有  --skip-grant-tables  类似的进程 找到其 PID kill掉
#然后在 kill掉其他进程
#PID : 用户名后面的 数字为PID
$kill -9 PID
```

- 正常启动mysql
```
#普通直接安装
$service mysqld start
```

- 登录测试
```
#输入密码
$mysql -uroot -p
```
