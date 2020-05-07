---
title: 'docker系统学习'
date: 2019-10-13T11:12:19+08:00
publishdate: 2019-10-13T11:12:19+08:00
lastmod: 2019-10-13T11:12:19+08:00
draft: false
tags: ['docker']
series: ['server']
categories: ['docker']
toc: true
summary: 'This is docker '
---
[官网](https://www.docker.com/)

[文档](https://docs.docker.com/reference/)

[Docker Hub](https://hub.docker.com/)

#### 架构和底层技术

> 1. docker提供了一个打包，开发，运行app的平台
> 2. 把app和底层infrastructure隔离开来

![image](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1570289397760&di=292a5716008a14c2643859e6046550fd&imgtype=0&src=http%3A%2F%2Faliyunzixunbucket.oss-cn-beijing.aliyuncs.com%2Fjpg%2F17c4de2ab18d053c6b95bbb6e5bce6de.jpg%3Fx-oss-process%3Dimage%2Fresize%2Cp_100%2Fauto-orient%2C1%2Fquality%2Cq_90%2Fformat%2Cjpg%2Fwatermark%2Cimage_eXVuY2VzaGk%3D%2Ct_100)

#### Docker Engine

> 1. 后台进程（dockerd）
> 2. REST API Server
> 3. CLI接口（docker）

```bash
# 查看后台运行的dockor程序
ps -ef | grep docker
```

<br>

#### Image

> 1. 文件和 mate data 的集合（root filesystem）
> 2. 分层的，并且每一层都可以添加改变，删除文件成为一个新的image
> 3. 不同的image可以共享相同的layer
> 4. Image本身是read-only的

```bash
# 查看已有的image
docker image ls

# 拉取官方demo
docker pull hello-world
# 执行
docker run hello-world
```

##### 1. 获取image

1. Build from Dockerfile
2. Pull from Registry(类似git)
3. 第三方images

```bash
# mak 解决 sodu 权限问题
# 加入group
sudo groupadd docker
group docker
docker version
# 重启docker
service docker restart
# 重新登录ssh主机
exit
# 登录
```


##### 2. 创建Base Image，基于c语言的可执行文件和Dockerfile（hello world）

1. 新建hello-world文件夹
```bash
mkdir hello-world
cd hello-world/
```

2. 编写hello.c
```bash
vim hello.c
#include<stdio.h>
int main()
{
  printf("hello docker\n");
}
:wq
```

3. 编译c语言 需要安装插件
``` bash
yum install gcc
yum install glibc-static
```

4. 生成可执行文件hello
```bash
gcc -static hello.c -o hello
```

5. 编写Dockerfile
```bash
vim Dockerfile

FROM scratch // 没有FROM
ADD hello /
CMD ["/hello"]
:wq
```

6. build 一个 Image
```bash
docker build -t 【docker hub的id/hello-world】 .

# 执行
docker run 【docker hub的id/hello-world】
```

7. 查看image的分层
```
# docker image ls 可见 image ID
docker history 【image ID】
```

8. 移除image
```bash
docker image rm 【image ID】
或
docker rmi 【image ID】
```

##### 3.Image发布

> - 往docker hub去上传image。需要创建一个docker hub账号

1. 登录
```
docker login
```
2. push
```
docker push 【你要push的image名字:latest】
```

<br>

#### container

> 1. 通過Image创建（copy）
> 2. 在Image layer之上建立一个container layer（可读写）
> 3. 类比面向对象： 类（image）和实例（container）
> 4. Image负责app的存储和分发，Container负责运行app

```
# 查看可执行的命令有哪些
docker image
docker container
```

1. 执行docker container
```bash
# 由于执行的不是一个常驻内存的进程，所以无结果
docker container ls
# 执行，可查看退出的container
docker container ls -a
或
docker ps -a
```

2. 交互式运行
```bash
docker run -it centos
# 查看运行的
docker container ls

# 退出 centos
exit

# 查看已退出的container
docker container ls -a
docker container ls -aq
或
docker ps -a    # 列出容器

# 移除
docker container rm 【container ID】
或
docker rm 【container ID】
# 批量移除
docker rm $(docker container ls -aq)
或
docker rm $(docker ps -aq)
# 按状态移除(有up 有exited)
docker rm $(docker container ls -f "status=exited" -q)
```

3. 操作运行中的容器
```bash
# 进入已运行的容器，里面执行shell
docker exec -it 【CONTAINER ID】/bin/bash
# 运行
pwd
或
ls
# 退出
exit 

docker exec -it 【CONTAINER ID】python
# 运行
print "hello py"
# 退出
exit

docker ps

# 打印正在运行的容器的ip地址
# -it: 让容器可以接受标准输入并分配一个伪tty
docker exec -it 【CONTAINER ID】ip a
```

4. 开启/停止container
```bash
docker start demo
docker stop 【CONTAINER ID】
```

5. 查看container详细信息
```bash
docker ps -a
docker inspect 【CONTAINER ID】
```

<br>

#### 构建自己的Docker镜像

方式一、 基于image产生新的image。（不提倡，容易下毒）
```bash
# 【拷贝的iamge Names】可以使用docker container ls -a 查看
docker commit 【拷贝的iamge Names】 【你自己命名的Names】
例如：
docker commit image-name cl/image-name-new
# 查看layer
docker history 【image ID】
```

方式二、 通过Dockerfile生成

1. 创建一个目录
```
mkdir centos-demo
cd centos-demo
vim Dockerfile
```

2. 编写Dockerfile
```
FROM centos 
# image是只读的，如何运行安装指令
RUN yum install -y vim
:wq

# 查看Dockerfile
cd centos-demo
more Dockerfile
```

3. build
```bash
# . 代表当前目录， t：代表tag
docker build -t cl/contos-vim .
```

<br>

#### Dockerfile详解

[docker-library](https://github.com/docker-library)

1. FROM

> 尽量使用官方的image，因为安全

```bash
FROM scratch        # 制作base image
FROM centos         # 使用base image
FROM ubuntu:14.04
```

2. LABEL

> 类似注释

```bash
LABEL maintainer="670199384@qq.com"
LABEL version="1.0"
LABEL description="描述"
```

3. RUN

> 执行命令，反斜线换行

```bash
RUN yum install && yum install -y vim \
    python-dev

RUN apt-get update && apt-get install -y perl \
    pwgen...
    
RUN /bin/bash -c 'source $HOME/.bashrc;echo $HOME'
```

4. WORKDIR

> 设定当前工作目录
> 1. 用WORKDIR，不要用RUN cd
> 2. 尽量使用绝对目录

```bash
WORKDIR /test   # 如果没有,会自动创建test目录
WORKDIR demo
RUN pwd         # 输出结果应该是/test/demo
```

5. ADD && COPY

> 添加和复制文件
> 1. 大部分情况，COPY优于ADD
> 2. ADD除了COPY还有额外功能（解压）
> 3. 添加远程文件/目录请使用curl或者wget

```bash
ADD hello /
ADD test.tar.gz /   # 添加到根目录并解压

WORKDIR /root
ADD hello test/     # /root/test/hello

WORKDIR /root
COPY hello test/     # /root/test/hello
```

6. ENV

> 多用，可增加可维护性

```bash
ENV NODE_ENV product        # 设置环境变量
RUN git push ${NODE_ENV}    # 引用常量
```

7. VOLUME 和 EXPOSE

> - 存储
> - EXPOSE（网络端口暴露）



8. CMD 和 ENTRYPOINT

> - CMD: 
>   - 设置容器启动后默认执行的命令和参数；
>   - docker run指定了其他命令，CMD命令会被忽略；
>   - 如果指定了多个CMD，只有最后一个会执行
> - ENTRYPOINT：
>   - 设置容器启动时运行的命令
>   - 让容器以应用程序或服务的形式运行
>   - 不会被忽略，一定会执行

9. Shell格式和Exec格式

```bash
FROM centos
ENV name Docker

# Shell格式
ENTRYPOINT echo "hello $name"
// 输出：hello Docker


# Exec格式
ENTRYPOINT ["/bin/echo", "hello $name"]
// 输出：hello $name
ENTRYPOINT ["/bin/echo", "-c", "echo hello $name"]
// 输出：hello Docker

```
