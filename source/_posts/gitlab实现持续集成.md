---
title: gitlab实现持续集成
date: 2018-04-27 16:13:29
categories: devops
tags:
  - devops
---

#### 背景

最近，公司搞了devops，在所负责的项目中，也搞过docker容器化部署，使用的是jenkins做为构建工具，公司采用是gitlab runner做为构建工具。
画了张项目结构图
![image](https://segmentfault.com/img/bV8gds?w=1119&h=626)

#### 结构体系
大致的流程
1. 提交代码到gitlab中，触发gitlab ci在runner机器中执行ci脚本，完成docker镜像的制作，push到私有部署的私有镜像库中

2. 在rancher中配置容器启动命令拉取镜像，服务启动

整个流程分为两步，第一步被称为CI(持续集成)，第二步被称为CD(持续部署)，在该方案中使用runner作为构建工具。使用rancher作为容器管理工具。

#### 组件介绍

**gitlab**
gitlab 是一个git的代码托管管理工具，具有强大的管理功能。在gitlab 8.0以后gitlab ci集成在gitlab中。通过在ci中注册的服务器来执行构建脚本。

**runner**
GitLab Runner 是一个开源项目， 它用来运行你定制的任务（jobs）并把结果返回给 GitLab。 GitLab Runner 配合GitLab CI（GitLab 内置的持续集成服务） 协调完成任务。

**docker私有镜像库**
docker私有镜像库用于存放docker的镜像，对镜像进行私有化部署.

**runcher**
runcher是一个开源的企业级容器部署及管理平台

#### 搭建
- 基础环境环境
主机名 | 用处 | 配置
--|--|--
gitlab  | gitlab | 2核4G
runner1 | runner | 1核4G
rancher-server | rancher| 2核4G
agent1 | agent1 | 1核2G
agent2 | agent2 | 1核2G

- 系统：
  使用ubuntu14.04，更换国内源

- docker安装
  将以上五台机器安装docker
  参考官网ubuntu14.04 下docker安装

- gitlab搭建
  在此使用docker进行gitlab的安装
  ```
  # 拉取gitlab镜像
  $docker pull gitlab/gitlab-ce:latest

  ＃ 启动docker容器
  # 创建/u1/gitlab用于将容器中的数据导出做持久化
  $mkdir /u1/gitlab
  $docker run \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name gitlab \
    --volume /u1/gitlab/config:/etc/gitlab \
    --volume /u1/gitlab/logs:/var/log/gitlab \
    --volume /u1/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce
  ```

  管理配置gitlab
  http://ip 就进入 gitlab 访问界面
  ![image](https://user-gold-cdn.xitu.io/2018/1/3/160bb50c34007f07?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

- runner安装及注册
```
＃安装
# For Debian/Ubuntu
$sudo apt-get install gitlab-ci-multi-runner

＃注册
$ sudo gitlab-ci-multi-runner register

Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com )
https://mygitlab.com/ci
Please enter the gitlab-ci token for this runner
xxx-xxx-xxx
Please enter the gitlab-ci description for this runner
my-runner
INFO[0034] fcf5c619 Registering runner... succeeded
Please enter the executor: shell, docker, docker-ssh, ssh?
docker
Please enter the Docker image (eg. ruby:2.1):
node:4.5.0
INFO[0037] Runner registered successfully. Feel free to start it, but if it's
running already the config should be automatically reloaded!
```
- docker私有镜像库搭建
```
$docker pull registry:2.1.1
$docker run -d -v /opt/registry:/var/lib/registry -p 5000:5000 --restart=always --name registry registry:2.1.1
```
- runcher服务部署
```
docker run -d --restart=unless-stopped -p 8080:8080 rancher/server
```
**rancher使用**

设置访问权限
由于任何访问服务器的人都可以打开Rancher界面，我们需要先进行权限控制，在系统管理-访问控制里选择local，设置个账号密码。

添加环境
添加多个环境分配给不同的用户权限，可以把测试环境、生产环境分离开进行管理，根据页面的说明操作即可。

添加主机
填写配置信息，主机上安装Rancher的客户端，让Rancher好发现并管理该主机

添加应用
docker化部署需要运行的系统

