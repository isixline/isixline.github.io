---
layout: post
title:  "docker 起步"
categories: docker
---

### 定义
Docker 属于 Linux 容器的一种封装，提供简单易用的容器使用接口。它是目前最流行的 Linux 容器解决方案。
容器是将操作系统层虚拟化，虚拟机则是虚拟化硬件。  
容器更多的用于表示软件的一个标准化单元。由于容器的标准化，因此它可以无视基础设施（Infrastructure）的差异，部署到任何一个地方。  

### Docker用途
1. 提供一次性的环境。比如，本地测试他人的软件、持续集成的时候提供单元测试和构建的环境。
2. 提供弹性的云服务。因为 Docker 容器可以随开随关，很适合动态扩容和缩容。
3. 组建微服务架构。通过多个容器，一台机器可以跑多个服务，因此在本机就可以模拟出微服务架构。

### image
docker把应用程序及其依赖，打包在image文件中。可以看作是容器的模版。
```
# 列出所有image
docker image ls
docker image list
docker images
```
```
# 删除image
docker rmi [imageName]
docker image rm [imageName]
```

### hello word
从仓库抓取image文件到本地
```
# library 组名
# hello-world image文件名
docker image pull library/hello-world
```
运行，生成一个容器实例
```
docker run container hello-world
```

### container
image生成的容器实例。
```
# 列出正在运行的容器
docker container ls
docker container list
docker ps
```
```
# 列出所有容器
docker container ls -a
docker container list -a
docker ps -a
```
```
# 停止容器（提供容器一定时间用于退出）
docker stop [containerId]
# 停止容器（直接退出）
docker kill [containerId]
```
```
# 删除容器文件
docker container rm [containerId]
docker rm [containerId]
```

### 制作docker容器
.dockerignore文件，排除不打包进image的路径
```
.git
node_modules
npm-debug.log
```
Dockerfile文件
```
FROM node:8.4：该 image 文件继承官方的 node image，冒号表示标签，这里标签是8.4，即8.4版本的 node。
COPY . /app：将当前目录下的所有文件（除了.dockerignore排除的路径），都拷贝进入 image 文件的/app目录。
WORKDIR /app：指定接下来的工作路径为/app。
RUN npm install：在/app目录下，运行npm install命令安装依赖。注意，安装后所有的依赖，都将打包进入 image 文件。
EXPOSE 3000：将容器 3000 端口暴露出来， 允许外部连接这个端口。
```
```
创建image文件
docker image build -t [imageName] [DockerfilePath]
```

### 发布image
```
# 更改image标签，加入userName和repository
docker image tag [imageName] [username]/[repository]:[tag]
```
```
# 登录docker
docker login
```

```
# 推送到docker仓库
docker image push [username]/[repository]:[tag]

```


### 其他有用命令
```
# 启动已经生成、已经停止运行的容器文件。
docker container start [containerId]
```
```
# 查看 docker 容器的输出
docker container logs
```
```
# 进入一个正在运行的 docker 容器
docker container exec [containerId]
```
```
# 从正在运行的 Docker 容器里面，将文件拷贝到本机
docker container cp [containID]:[/path/to/file] [outputPath]
```