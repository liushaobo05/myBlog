---
title: docker命令行清单
date: 2018-05-04 13:15:26
categories: docker
tags:
  - docker
---
#### 1.环境信息相关
- **info**

使用方法：docker info

使用说明:

显示本地docker配置信息

- **version**

使用方法：docker version

使用说明：

显示 Docker 的版本号，API 版本号，Git commit，Docker 客户端和后台进程的 Go 版本号。

#### 2.系统运维相关
- **attach**

使用方法：

docker attach [OPTIONS] CONTAINER

使用说明：

在开发应用的过程中运用这个命令可以随时观察容器內进程的运行状况

- **build**

使用方法：

docker build [OPTIONS] PATH | URL | -

使用说明：

这个命令是从源码构建新 Image 的命令

- **commit**

使用方法：

docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

使用说明：

这个命令的用处在于把有修改的 container 提交成新的 Image，然后导出此 Imange 分发给其他场景中调试使用。

- **cp**

使用方法：

cp CONTAINER:PATH HOSTPATH

使用说明：

使用 cp 可以把容器內的文件复制到 Host 主机上

- **diff**

使用方法：

docker diff CONTAINER

使用说明：

diff 会列出3种容器内文件状态变化

- **images**

使用方法：

docker images [OPTIONS] [NAME]

使用说明：

显示镜像列表

- **export/ import / save / load**

使用方法：

```
docker export red_panda > latest.tar
docker import URL|- [REPOSITORY[:TAG]]
docker save IMAGE
docker load
```

使用说明：

导出或加载容器的镜像文件

- **inspect**

使用方法：

```
docker inspect CONTAINER|IMAGE [CONTAINER|IMAGE...]
```

使用说明：

查看容器运行时详细信息的命令

- **kill**

使用方法：

docker kill [OPTIONS] CONTAINER [CONTAINER...]

使用说明：

杀掉容器的进程

- **port**

使用方法：

docker port CONTAINER PRIVATE_PORT

使用说明：

打印出 Host 主机端口与容器暴露出的端口的 NAT 映射关系

- **pause / unpause**

使用方法：

docker pause CONTAINER

使用说明：

使用 cgroup 的 freezer 顺序暂停、恢复容器里的所有进程

- **ps**

使用方法：

docker ps [OPTIONS]

使用说明：

docker ps 打印出正在运行的容器

- **rm**

使用方法：

docker rm [OPTIONS] CONTAINER [CONTAINER...]

使用说明：

删除指定的容器

- **rmi**

使用方法：

docker rmi IMAGE [IMAGE...]

使用说明：

指定删除 Image 文件

- **run**

使用方法：

docker run [OPTIONS] IMAGE [COMMAND] [ARG...]

使用说明：

这个命令是核心命令，可以配置的子参数详细解释可以通过 docker run --help 列出。

- **start / stop / restart**

使用方法：

docker start CONTAINER [CONTAINER...]

使用说明：

这组命令可以开启（两个：start，restart），停止（一个：stop）一个容器。

- **tag**

使用方法：

docker tag [OPTIONS] IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]

使用说明：

组合使用用户名，Image 名字，标签名来组织管理Image

- **top**

使用方法：

docker top CONTAINER [ps OPTIONS]

使用说明：

显示容器內运行的进程。

- **wait**

使用方法：

docker wait CONTAINER [CONTAINER...]

使用说明：

阻塞对指定容器的其他调用方法，直到容器停止后退出阻塞。

- **rename**

使用方法：

docker rename CONTAINER NEW_NAME

使用说明：

重新命名一个容器。

- **stats**

使用方法：

docker stats [OPTIONS] [CONTAINER...]

使用说明：

实时显示容器资源使用监控指标。

- **update**

使用方法：

docker update [OPTIONS] CONTAINER [CONTAINER...]

使用说明：

更新一或多个容器实例的 IO、CPU、内存，启动策略参数。

- **exec**

使用方法：

docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

使用说明：

在运行中容器中运行命令。

- **deploy**

使用方法：

docker deploy [OPTIONS] STACK

使用说明：

部署新的 stack 文件，两种格式 DAB 格式和 Compose 格式

- **create**

使用方法：

docker create [OPTIONS] IMAGE [COMMAND] [ARG...]

使用说明：

可以创建容器但并不执行它。

#### 3.日志信息相关

- **events**

使用方法：

docker events [OPTIONS]

使用说明：

打印容器实时的系统事件。

- **history**

使用方法：

docker history [OPTIONS] IMAGE

使用说明：

打印指定 Image 中每一层 Image 命令行的历史记录。

- **logs**

使用方法：

docker logs CONTAINER

使用说明：

批量打印出容器中进程的运行日志。

#### 4.Docker Hub服务相关

- **login/ logout**

使用方法：

docker login [OPTIONS] [SERVER]
docker logout [SERVER]

使用说明：

登录登出 Docker Hub 服务。

- **pull / push**

使用方法：

docker push NAME[:TAG]

使用说明：

通过此命令分享 Image 到 Hub 服务或者自服务的 Registry 服务。

- **search**

使用方法：

docker search TERM

使用说明：

通过关键字搜索分享的 Image

#### 5. 后台进程


