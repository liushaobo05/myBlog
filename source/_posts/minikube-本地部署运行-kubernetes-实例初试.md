---
title: minikube 本地部署运行 kubernetes 实例初试
date: 2018-05-04 17:42:19
categories: kubernetes
tags:
  - k8s
---
> k8s作为目前最火的容器集群管理工具，总让人蠢蠢欲试，但一搞到集群总会让人望而止步，多台基础设施环境的主机，如果没有丰富的运维经验，很难驾驭的了。好在，k8s发布了一个迷你版的环境，可以直接跑在我们的电脑上。

那么，就一起来学习下这个迷你版的k8s吧

#### kubernetes 介绍
** kubernetes是什么?**

>  Kubernetes 是 Google 开源的容器集群管理系统，它构建在目前流行的 Docker 技术之上，为容器化的应用提供资源调度、部署运行、服务发现、扩容缩容等一整套功能。

** Minikube是什么?**

> Minikube 是一个可以在本地轻松运行 Kubernetes 的工具。Minikube 会在您的笔记本电脑中的虚拟机上运行一个单节点的 Kubernetes 集群，以便用户对 Kubernetes 进行试用或者在之上进行 Kubernetes 的日常开发。

#### 实验目标

1. 在Mac OS 10.11.6系统下安装Minikube
2. 在Minikube上部署一个应用
3. 对应用做持续更新

#### minikube 安装
- 使用 curl 下载和安装 Minikube 最新发布的版本
```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64 && \
  chmod +x minikube && \
  sudo mv minikube /usr/local/bin/
```

- 安装 Hypervisor
```
brew install docker-machine-driver-xhyve
sudo chown root:wheel $(brew --prefix)/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve
sudo chmod u+s $(brew --prefix)/opt/docker-machine-driver-xhyve/bin/docker-machine-driver-xhyve
```

**注意**
以上下载有被墙的风险，被墙，请寻找其它下载源进行安装

#### kubectl 安装
- 通过 Homebrew 安装
```
brew install kubectl
```

#### 安装docker
参考docker官网进行安装，此处不在分解

#### 启动minikube
- 启动minikube
```
minikube start --vm-driver=xhyve
```

- 设置 Minikube 上下文
```
kubectl config use-context minikube
```

- 验证
```
kubectl cluster-info
```

**注意：**

若启动失败，可以执行rm -rf ~/.minikube，重新安装

#### 创建应用镜像

- 创建应用
代码使用文件 server.js 保存在 hellonode 的文件夹中

```
var http = require('http');

var handleRequest = function(request, response) {
  console.log('Received request for URL: ' + request.url);
  response.writeHead(200);
  response.end('Hello World!');
};
var www = http.createServer(handleRequest);
www.listen(8080);

```

- 建 Ddocker 容器镜像
```
FROM node:6.9.2
EXPOSE 8080
COPY server.js .
CMD node server.js
```

- 设置Minikube Docker 守护程序
```
eval $(minikube docker-env)
```

**注意：**

当您不再希望使用这个 Minikube 主机时，您可以通过运行 eval $(minikube docker-env -u) 来撤消此更改。

- 构建镜像
```
docker build -t hello-node:v1 .
```

#### 部署
- 创建 Deployment
```
$kubectl run hello-node --image=hello-node:v1 --port=8080

#查看 Deployment：
$kubectl get deployments
NAME         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
hello-node   1         1         1            1           3m

# 查看 Pod：
$kubectl get pods
NAME                         READY     STATUS    RESTARTS   AGE
hello-node-714049816-ztzrb   1/1       Running   0          6m
```

- 创建 Service

默认情况下，Pod 只能通过 Kubernetes 集群中的内部 IP 地址访问。要使得 hello-node 容器可以从 Kubernetes 虚拟网络的外部访问，您必须将 Pod 暴露为 Kubernetes Service。

在您的开发机器中，可以使用 kubectl expose 命令将 Pod 暴露给公网：

```
kubectl expose deployment hello-node --type=LoadBalancer
```

-  访问服务
```
minikube service hello-node
```

- 查看应用日志
```
$kubectl logs <POD-NAME>
```


#### 总结
以上，安装过程中，遇到的一些问题，大都和网络有关，最好使用科学上网，以免安装不顺利

本人在安装过程中，遇到一个问题，发现 hello-minikube 服务并没有成功启动
```
$ kubectl get pods
NAME                             READY     STATUS              RESTARTS   AGE
hello-minikube-598805112-3bzmf   0/1       ContainerCreating   0          15s
```

发现 hello-minikube 的状态一直是 ContainerCreating，并且 READY 为 0/1

通过minikube logs查看发现
gcr.io/google_containers/pause-amd64:3.0这个镜像下载失败，被墙的原因吧

这个本地设置有代理，可能命令行的原因吧

- 替代方案
```
# 替换镜像
$ docker pull visenzek8s/pause-amd64:3.0
$ docker tag visenzek8s/pause-amd64:3.0 gcr.io/google_containers/pause-amd64:3.0

# 显式设置拉取策略为 IfNotPresent
$ kubectl run hello-node --image=hello-node:v1 --port=8080 --image-pull-policy=IfNotPresent
```


