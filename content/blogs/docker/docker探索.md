---
title: 'docker探索'
date: '2018-06-10T20:42:11+08:00'
publishdate: '2018-06-10T20:42:11+08:00'
lastmod: '2018-06-10T20:42:11+08:00'
draft: false
tags: ['server', 'blog', 'docker']
series: ['server']
categories: ['docker']
img: 'images/blog/img1.jpg'
toc: true
summary: 'This is an example of adding an intro picture to the post. '
---

## docker

> 镜像（Image）+ 容器（Container）+ 仓库（Repository）

#### 下载：

linux：
sudo wget -qO- http:get.docker.com | sh
sudo usermod -aG docker xbf // 不用 sodu
windows：
官网下载：https://docs.docker.com/engine/installation/

#### 命令行：

```bash
#是否docker安装成功
$ docker run hello-world

#查看docker信息
$ docker info
$ docker -v

#获取镜像（使用别人的镜像）
$ docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
# 例子：docker pull ubuntu:16.04 （不给用户名，默认为library，获取官方镜像）

#运行
1.$ docker run -it --rm ubuntu:16.04 bash
2.$ winpty docker run -it --rm ubuntu:16.04 bash
# 安装git bash的时候配置选取MinTTY/Windows
# 如果您选择了“使用MinTTY”选项，您的Bash pompt将被托管在MinTTY终端模拟器中，而不是使用Windows的CMD控制台。
# MinTTY终端模拟器与Windows控制台程序不兼容，除非您使用winpty对命令进行前缀
参数讲解：
    -it 这是两个参数，一个是 -i ：交互式操作，一个是 -t 终端。我们这里打算进入
    bash 执行一些命令并查看返回结果，因此我们需要交互式终端。
    --rm ：这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容
    器并不会立即删除，除非手动 docker rm 。我们这里只是随便执行个命令，看看结果，
    不需要排障和保留结果，因此使用 --rm 可以避免浪费空间。
    bash ：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 bash 。

3. exit(退出)

#列出镜像
$ docker images

#查看定制的镜像
$ docker image ls nginx

#具体查看镜像内的历史记录
$ docker history nginx:v2

#列出运行中的镜像(-a所有)
$ docker ps [-a]

# 停止
$ docker stop

#删除镜像
$ docker image rm [选项] <镜像1> [<镜像2> ...] --force
```

### 利用 commit 理解镜像构成

> 镜像是容器的基础，每次执行 docker run 的时候都会指定哪个镜像作为容器运行的基础。
> 在之前的例子中，我们所使用的都是来自于 Docker Hub 的镜像。直接使用这些镜像是可以满
> 足一定的需求，而当这些镜像无法直接满足需求时，我们就需要定制这些镜像。

#### 创建自己的镜像，即定制镜像

```bash
运行nginx
docker run -p 80:80 -d
```

```bash
<!-- 修改方式1 -->
# copy的形式替换usr/share/nginx/html/index.html
$ docker cp index.html 4e5bfcb57f98://usr/share/nginx/html

<!-- 修改方式2 -->
#修改nginx显示内容
docker exec -it webserver bash
#我们修改了容器的文件，也就是改动了容器的存储层。我们可以通过 docker diff 命令看到
具体的改动。
$ docker diff webserver
```

方式 1：

```bash
#提交
docker commit -m "备注" [Id] [name]

# 例子：
docker commit \
--author "Tao Wang <twang2218@gmail.com>" \
--message "修改了默认网页" \
webserver \
nginx:v2
```

方式 2(更好)：

```bash
$ mkdir mynginx
$ cd mynginx
$ touch Dockerfile
#内容
FROM nginx
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
#涉及到了两条指令， FROM 和 RUN

#发布
$ docker build -t [name] .
$ docker run -d -p 80:80 [name]
$ docker stop [name]
```
