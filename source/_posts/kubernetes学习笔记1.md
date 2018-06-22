---
title: kubernetes学习笔记1
date: 2018-05-22 20:47:54
categories: kubernetes
tags:
  - k8s
---
#### 从helloworld入手
学习一项IT新技术，都会从helloworld起步，那么k8s的helloworld是什么样子呢？运行一个hello world首先要安装针对这项技术的IT环境，在本机安装minikube的单点集群实验。

我们将按照以下几步进行展开
1. 创建一个集群
2. 部署一个应用
3. 创建service，外部访问
4. 伸缩应用副本
5. 版本回退

#### 集群管理
通过minikube安装的单节点的集群，首先通过minikube 创建一个集群
```
$minikube version
minikube version: v0.23.0

Usage:
  minikube [command]

Available Commands:
  addons           Modify minikube's kubernetes addons
  completion       Outputs minikube shell completion for the given shell (bash)
  config           Modify minikube config
  dashboard        Opens/displays the kubernetes dashboard URL for your local cluster
  delete           Deletes a local kubernetes cluster
  docker-env       Sets up docker env variables; similar to '$(docker-machine env)'
  get-k8s-versions Gets the list of available kubernetes versions available for minikube
  ip               Retrieves the IP address of the running cluster
  logs             Gets the logs of the running localkube instance, used for debugging minikube, not user code
  mount            Mounts the specified directory into minikube
  profile          Profile sets the current minikube profile
  service          Gets the kubernetes URL(s) for the specified service in your local cluster
  ssh              Log into or run a command on a machine with SSH; similar to 'docker-machine ssh'
  ssh-key          Retrieve the ssh identity key path of the specified cluster
  start            Starts a local kubernetes cluster
  status           Gets the status of a local kubernetes cluster
  stop             Stops a running local kubernetes cluster
  update-context   Verify the IP address of the running cluster in kubeconfig.
  version          Print the version of minikube

# 1. 启动一个集群
$minikube start
Starting local Kubernetes v1.8.0 cluster...
Starting VM...
Getting VM IP address...
Moving files into cluster...
Setting up certs...
Connecting to cluster...
Setting up kubeconfig...
Starting cluster components...
Kubectl is now configured to use the cluster.

# 2. 获取集群版本
$kubectl version
Client Version: version.Info{Major:"1", Minor:"8", GitVersion:"v1.8.4", GitCommit:"9befc2b8928a9426501d3bf62f72849d5cbcd5a3", GitTreeState:"clean", BuildDate:"2017-11-20T19:11:22Z", GoVersion:"go1.9.2", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"8", GitVersion:"v1.8.0", GitCommit:"0b9efaeb34a2fc51ff8e4d34ad9bc6375459c4a4", GitTreeState:"dirty", BuildDate:"2017-10-17T15:09:55Z", GoVersion:"go1.8.3", Compiler:"gc", Platform:"linux/amd64"}

# 3. 查看当前集群信息
$kubectl cluster-info

# 3. 获取节点信息
$kubectl get nodes
```

#### 应用部署
部署一个nigix容器
```
$kubectl run my-nginx --image=nginx --port=80

# 2. 查看deployment
$kubectl get deployments

# 3. 设置集群内部网路代理
$kubectl proxy

# 4. 查看代理信息
$curl http://localhost:8001/version
{
  "major": "1",
  "minor": "8",
  "gitVersion": "v1.8.0",
  "gitCommit": "0b9efaeb34a2fc51ff8e4d34ad9bc6375459c4a4",
  "gitTreeState": "dirty",
  "buildDate": "2017-10-17T15:09:55Z",
  "goVersion": "go1.8.3",
  "compiler": "gc",
  "platform": "linux/amd64"
}

# 5. pod name
$export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME

# 6. 访问请求
$curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/

# 7. 进入容器
$kubectl exec -ti $POD_NAME bash
```

#### 创建service
```
# 创建service
$kubectl expose deployment my-nginx --type="NodePort" --port 80

# 设置环境变量
$export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
$echo NODE_PORT=$NODE_PORT

＃ 测试
$curl $(minikube ip):$NODE_PORT
```

#### 伸缩应用
```
# 伸缩应用
$kubectl scale deployments/my-nginx --replicas=4

# 查看节点
$kubectl get pods -o wide

# kubectl scale deployments/my-nginx --replicas=2
```

#### 回滚应用
```
kubectl rollout undo deployments/my-nginx
```

