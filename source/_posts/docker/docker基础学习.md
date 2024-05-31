---
title: docker基础学习
tags: [运维]
categories: [docker]
description: docker基础学习
date: 2022-07-28
cover: https://raw.githubusercontent.com/kangkang123269/hexo-blog-code/main/assets/post/docker.jpeg
---

# docker 基础学习——（篇一）

### 安装 Docker

- Mac：[download.docker.com/mac/stable/…](https://download.docker.com/mac/stable/Docker.dmg)
- Windows：[download.docker.com/win/stable/…](https://download.docker.com/win/stable/Docker for Windows Installer.exe)
- Linux：[get.docker.com/](https://get.docker.com/)

### Docker 的组成部分

- 镜像(image)
  - docker 镜像就好像是一个模板，可以通过这个模板来创建出来容器的服务，mysql 镜像--->run 命令--->mysql 容器(提供 mysql 服务)
  - 通过这个镜像可以创建多个容器(最终服务运行或者项目运行就在容器中的)
- 容器(container)
  - Docker 利用容器技术，独立运行一个或者一个组应用，通过镜像进行创建
  - 启动，停止，删除，基本命令
  - 目前就可以把这个容器理解为就是一个简易的 linux 系统
- 仓库(repository)
  - 仓库就是存放镜像的地方
  - 仓库分为公有仓库和私有仓库
  - Docker Hub(默认是国外的)
  - 阿里云……都有容器服务器(配置镜像加速)

#### **docker 的运行**

- run 命令执行后到底发生了什么？

  **在执行了 docker 这个命令后，从 client 端发送到 server 这个服务端，在服务中进行执行，这样首先 docker 会在本地进行查找，看看镜像是不是存在，存在直接运行，不存在的话，会发送指令到 docker-hub 中，进行查找，若找到了就进行下载，若没有找到，会从服务端返回错误信息，假设找到了，下载到本地，本地就会加载这个镜像，运行起来。**

- **底层原理**

  - docker 是一个 Client-Server 结构的系统，docker 的守护进程运行在主机上，通过 socket 从客户端进行访问
  - Docker-server 接收到 docker-client 的指令，就会执行这个命令

### docker 一些基础的命令

#### 镜像命令

- 查看所有的镜像列表

```
docker images
```

- 搜索镜像

```
docker search 镜像名称
```

- 下载镜像

```
docker pull 镜像名称
docker pull 镜像名称:tag
```

- 删除镜像

```
rmi -------remove image
Docker rmi -f 镜像id/镜像名称                删除一个镜像
Docker rmi -f 镜像id 镜像id  镜像id        删除多个镜像
Docker rmi -f  $(docker images -aq)     删除所有镜像
```

#### 容器命令

- **下载容器**

```
docker pull centos
```

- **新建容器并启动**

```
Docker run [可选参数] image
```

可选参数：

- `--name = "Name"` 给容器起名字 centos1 centos2 用来区分容器
- `-d` 后台的方式进行运行
- `-it` 使用交互式的运行方式，进入容器后查看内容
- `-p` 指定容器的端口 -p 8080：8080
  - 四种端口的指定方式
    - -p ip:主机端口:容器端口
    - **-p** **主机端口：容器端口** **（最常用）**
    - -p 容器端口
    - 端口号
- **列出所有运行的容器**
  - `无参数` 列出当前正在运行的容器
  - `-a` 列出当前正在运行的容器+带出历史运行过的容器
  - `-n=？` 显示最近创建的容器
  - `-q` 只显示容器的编号
- **退出容器**
  - Exit 直接容器停止并且退出了
  - Ctrl+Q 容器不停止但是退出了
- **删除容器**
  - `Docker rm` 容器 id
  - `Docker rm` 容器 id 容器 id(删除多个)
  - `Docker rm -f $(docker ps -aq)`
    - Docker ps -a -q|xargs docker rm 管道的方式删除所有的容器
    - -f 是强制删除
- **启动和停止容器的操作**
  - 启动
    - docker start 容器 id
  - 重启容器
    - docker restart 容器 id
  - 停止容器
    - docker stop 容器 id
  - 杀掉容器
    - docker kill 容器 id

#### 其他常见命令

- 后台启动容器

  - `Docker run -d` 镜像名
    - 问题：docker ps 发现 centos 停止了
    - 常见的坑：docker 容器使用后台运行，就必须要有一个前台进程，docker 发现没有应用，就会自动提停止
      - 通俗的理解就是：我们在 run centos 的时候，之前会切换到容器之中让我们进行操作，但是-d 直接从后台进行启动，docker 会检测到这个部分是没有人操作或者容器没有动静，自己就会将相应的资源进行停止
      - 所以 nginx 容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了

- 查看日志

  - Docker logs -tf --tail 10 容器 id

- 测试

  - 自己编写一段 shell 脚本让程序在后台自己跑
    - Docker run -d centos /bin/sh -c "while true;do echo hahaha;sleep 1;done"
  - docker ps
  - Docker logs -tf --tail 10 容器 id

- 查看容器内部的进程信息

  - top 命令
    - docker top 容器 id

- 查看容器的元数据

  - docker inspect 容器 id

- 进入当前正在运行的容器

  - exec
    - 通常情况下的容器都是在后台方式运行的，需要我们进入容器 ，修改一些配置

- 查看历史命令

```
docker history 镜像id
```

#### 分层理解

- 分层的镜像
  - 我们可以去下载一个镜像，注意观察下载日志的输出，可以看到的是一层一层的下载
- 思考：为什么 Docker 镜像要采用这种分层的结构那？
  - 最大的好处，我觉得莫过于资源共享！比如有多个镜像团队都从相同的 Base 镜像构建而来，那么宿主机只需要在磁盘中保留一份 base 镜像，同时内存中也只需要加载一份 base 镜像，这样就可以为所有的容器服务了，而且镜像的每一层都可以被共享。

#### commit 镜像

Docker commit 提交容器成为一个新的副本

```
Docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像:[Tag]
```

- 实战测试
  - 启动一个默认的 tomcat
  - 发现这个 tomcat 是没写 webapps 应用的，镜像的原因，官方镜像默认 webapps 下面是没有东西的
  - 自己拷贝文件进去
  - 将我们修改过的镜像提交成一个新的镜像

> 推荐学习：https://juejin.cn/post/6977180684376866847
