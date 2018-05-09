---
title: Linux下命令集
date: 2018-05-03 18:20:38
categories: linux
tags:
  - linx cli
---
linux命令行作为开发运维一项必备的技能，熟练的使用命令行工具，可以提高日常开发效率和分析排错的能力。此文档主要为平时常使用的一些命令集合整理，方便自己查阅。

#### git相关的
```
# 打包增量代码
$git archive --format=tar.gz --output ~/www-csrf.tar.gz HEAD $(git diff 66fa65b6499f860d88a7e69698454deddcbd5db6 a6f8faadf9dfce35edd044b76baba2ad91adc3ec --name-only)
```

#### 网络排错
```
# 查看端口使用情况
$netstat -tunpl

# 网络链接状态检查
$nc ip port

#追踪dns解析
$nslookup www.baidu.com
Server:		202.96.209.5
Address:	202.96.209.5#53

Non-authoritative answer:
www.baidu.com	canonical name = www.a.shifen.com.
Name:	www.a.shifen.com
Address: 115.239.211.112
Name:	www.a.shifen.com
Address: 115.239.210.27

```

#### 系统处理

##### 修改国内源
```
$cp /etc/apt/sources.list /etc/apt/sources.list_backup
$cat > /etc/apt/sources.list << EOF
#aliyun
deb http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
EOF

# 更换源
$sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/mirrors\.163\.com\/ubuntu\//g' /etc/apt/sources.list
```

##### node.js项目脚本启动文件
```
#!/bin/bash

log_file="$PWD/auto-update-server.log"

function log() {
    echo '' 2>&1 | tee -a $log_file
    echo -e "\033[1;36m$1\033[1;0m" 2>&1 | tee -a $log_file
}

run_time=`date "+%Y-%m-%d %H:%M:%S"`
log "===== 脚本启动 ($run_time)====="

git checkout .

log '更新remote'
git remote update 2>&1 | tee -a $log_file
git fetch --tags  2>&1 | tee -a $log_file

if [ $# -eq 1 ]; then
    log "切换到分支：$1"
    git checkout $1 2>&1 | tee -a $log_file
fi

log '获取最新代码'
git pull 2>&1 | tee -a $log_file

log '安装第三方包'
npm install 2>&1 | tee -a $log_file

log "关闭内部站服务器"
pm2 delete internal 2>&1 | tee -a $log_file

log '启动内部站服务器'
pm2 start app/app.js --name internal 2>&1 | tee -a $log_file

sleep 5
log '当前内部站服务器状态：'
pm2 status 2>&1 | tee -a $log_file

```

##### go语言修改工作目录

```
#!/usr/bin/env bash

set -e

if [ ! -f install.sh ]; then
        echo 'install must be run within its container folder' 1>&2
        exit 1
fi

CURDIR=`pwd`
OLDGOPATH="$GOPATH"
export GOPATH="$CURDIR"
export GOBIN=

if [ ! -d log ]; then
        mkdir log
fi

gofmt -w -s src

go install dreamgo

export GOPATH="$OLDGOPATH"

echo 'finished'
```

#### mysql脚本
```
#开启远程登录
grant all privileges on *.* to 'user'@'%' identified by 'passwd' with grant option;

#创建数据库
create database DB;

#创建用户
insert into mysql.user(Host,User,Password) values("localhost","user",password("passwd"));

#删除用户
DELETE FROM user WHERE user="username" and HOST="localhost";

#修改指定用户密码
update mysql.user set password=password('new passwd') where user="username" and host="localhost";

#用户授权
grant all privileges on DB.* to 'user'@'localhost' identified by 'passwd';
grant select,update on DB.* to 'user'@'localhost' identified by 'passwd';

#刷新权限
flush privileges;

#数据库导出
mysqldump -uUSRENAME -pPASSWD DATABASE > DATABASE.sql

#数据库导出(只导出表结构 -d)
mysqldump -uUSRENAME -pPASSWD -d DATABASE > DATABASE.sql

#数据库导入

#1.切换数据库
use DATABASE;
#2.设置编码
set names utf8;
#3.执行导入操作
source /home/abc/abc.sql;

#直接导入
mysql -uUSERNAME -p DATABASE < DATABASE.sql
```

#### shell编程
