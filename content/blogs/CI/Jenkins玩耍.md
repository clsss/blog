---
title: "Jenkins"
date: 2019-10-13T11:12:19+08:00
publishdate: 2019-10-13T11:12:19+08:00
lastmod: 2019-10-13T11:12:19+08:00
draft: false
toc: true
summary: "This is Jenkins"
---

### centos下搭建Jenkins持续集成环境(安装jenkins)

#### 1. 前提条件

```bash
# 安装JDK
yum install -y java

# 安装jenkins
# 方式1（说实话：慢！）
wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
yum install -y jenkins

# 方式2（如果不能安装就到官网下载jenkis的rmp包）
wget http://pkg.jenkins-ci.org/redhat-stable/jenkins-2.7.3-1.1.noarch.rpm
rpm -ivh jenkins-2.7.3-1.1.noarch.rpm
```

#### 2. 配置jenkins

```bash
vi /etc/sysconfig/jenkins

# 修改端口号，改成你想要设置的端口号（默认为8080）。
JENKINS_PORT="8080"
JENKINS_USER="jenkins"
JENKINS_HOME="/var/lib/jenkins"

```

#### 3. 启动jenkins

```bash
service jenkins start/stop/restart
```

> 1. 安装成功后Jenkins将作为一个守护进程随系统启动
> 2. 系统会创建一个“jenkins”用户来允许这个服务，如果改变服务所有者，同时需 要修改/var/log/jenkins, /var/lib/jenkins, 和/var/cache/jenkins的所有者
> 3. 启动的时候将从/etc/sysconfig/jenkins获取配置参数
> 4. 默认情况下，Jenkins运行在8080端口，在浏览器中直接访问该端进行服务配置
> 5. Jenkins的RPM仓库配置被加到/etc/yum.repos.d/jenkins.repo



#### 4. 打开jenkins 

```bash
访问：
{主机ip}:{配置的端口号}
例如：
127.0.0.1:8080

初始密码在：
/var/lib/jenkins/secrets/initialAdminPassword 
```

#### 5. 安装插件（Install suggested plugins-新手版）


#### 6. 创建管理员账号

```bash
访问: http://59.110.224.63:8080/
账号: caolin
密码: ***
```

#### 7. 创建秘钥

```bash
cd ~
ssh-keygen -t rsa


# /root/.ssh/id_rsa
# /root/.ssh/id_rsa.pub
```

#### 8. jenkins 软连接设置

```bash
# 查看pm2安装路径
which pm2   # 输出/usr/local/n/versions/node/10.16.3/bin/pm2
# 设置软连接
ln -s /usr/local/n/versions/node/10.16.3/bin/pm2 /usr/local/bin/pm2
```

<br><br><br><br>

参考：
1. https://www.cnblogs.com/loveyouyou616/p/8717177.html
2. https://www.cnblogs.com/reblue520/p/7130914.html
