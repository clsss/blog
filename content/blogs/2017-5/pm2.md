---
title: 'pm2'
date: '2017-05-25T22:13:01+08:00'
publishdate: '2017-05-25T22:54:11+08:00'
lastmod: '2017-05-25T22:54:11+08:00'
draft: false
tags: ['pm2', 'web']
series: ['web']
categories: ['pm2']
img: 'images/blog/img.jpg'
toc: true
summary: 'What is PM2'
---

## pm2 是什么？

> pm2 是一个带有负载均衡功能的 Node 应用的进程管理器.当然也支持性能监控，进程守护，负载均衡等功能。当你要把你的独立代码利用到全部的服务器上的所有 CPU，并保证进程永远都活着，0 秒的重载， PM2 是完美的。它非常适合 IaaS 结构，但不要把它用于 PaaS 方案（随后将开发 Paas 的解决方案）.

-   官网：http://pm2.keymetrics.io/docs/usage/quick-start/

> 需要小心.ssh 和一些翻墙问题

####特性：

1、内建负载均衡（使用 Node cluster 集群模块）
2、后台运行
3、0 秒停机重载
4、具有 Ubuntu 和 CentOS 的启动脚本
5、停止不稳定的进程（避免无限循环）
6、控制台检测
7、提供 HTTP API
8、远程控制和实时的接口 API ( Nodejs 模块,允许和 PM2 进程管理器交互 )

#### 安装（全局）

```bash
npm install -g pm2
```

#### 常用命令一览

普通模式

```
$ npm install pm2 -g            # 安装 PM2
$ pm2 start app.js              # 启动，守护进程，自动重启应用程序 Start, Daemonize and auto-restart application (Node)
$ pm2 start app.py              # 启动，守护进程，自动重启python应用程序 Start, Daemonize and auto-restart application (Python)
$ pm2 start npm -- start        # 启动，守护进程，自动重启node应用程序 Start, Daemonize and auto-restart Node application
```

集群模式 (只支持 node 进程) Cluster Mode (Node.js only)

```bash
# 在集群模式下，启动4个应用程序实例 Start 4 instances of application in cluster mode
# 同时，将网络请求，负载均衡到每个应用实例 It will load balance network queries to each app
$ pm2 start app.js -i 4

$ pm2 reload all               # 0秒重启所有应用 Zero Second Downtime Reload
$ pm2 scale [app-name] 10      # 将应用进程调整到10 Scale Cluster app to 10 process
```

进程监控 Scale Cluster app to 10 process

```bash
$ pm2 list               # 列出所有用PM2启动的进程 List all processes started with PM2
$ pm2 monit              # 显示每个应用占用的cpu和内存 Display memory and cpu usage of each app
$ pm2 show [app-name]    # 显示某个进程的所有信息 Show all informations about application
```

日志管理 Log management

```bash
$ pm2 logs              # 显示所有应用的日志 Display logs of all apps
$ pm2 logs [app-name]   # 显示某个应用的日志 Display logs for a specific app
$ pm2 logs --json       # json 化日志 Logs in JSON format
$ pm2 flush
$ pm2 reloadLogs
```

进程状态管理 Process State Management

```bash
$ pm2 start app.js --name="api" # 启动一个应用并命名为 api。 Start application and name it "api"
$ pm2 start app.js -- -a 34     # 启动一个应用，并传递“-a 34”的参数。 Start app and pass option "-a 34" as argument
$ pm2 start app.js --watch      # 重启一个应用，当文件改变的时候。Restart application on file change
$ pm2 start script.sh           # 启动一个 bash 脚本。Start bash script
$ pm2 start app.json            # 启动在 app.json 中声明的所有应用。Start all applications declared in app.json
$ pm2 reset [app-name]          # 重置所有计数器。Reset all counters
$ pm2 stop all                  # 停止所有应用。Stop all apps
$ pm2 stop 0                    # 停止 id 为 0 的应用。Stop process with id 0
$ pm2 restart all               # 重启所有应用。Restart all apps
$ pm2 gracefulReload all        # 在集群模式下，平稳的重加载所有应用。Graceful reload all apps in cluster mode
$ pm2 delete all                # 杀掉所有应用。Kill and delete all apps
$ pm2 delete 0                  # 杀掉 id 为 0 的进程。Delete app with id 0
```

启动／引导管理 Startup/Boot management

```bash
$ pm2 startup     # 检测 init 系统，在启动时生成和配置 pm2。Detect init system, generate and configure pm2 boot on startup
$ pm2 save        # 保存当前进程列表。Save current process list
$ pm2 resurrect   # 恢复以前保存的进程。Restore previously save processes
$ pm2 unstartup   # 停用和删除启动系统。Disable and remove startup system
$ pm2 update      # 保存进程，终止 PM2 并恢复进程。Save processes, kill PM2 and restore processes
$ pm2 generate    # 生成样本 json 配置文件。Generate a sample json configuration file
```

部署 Deployment

```bash
$ pm2 deploy app.json prod setup    # 设置“生产环境”远程服务器。 Setup "prod" remote server
$ pm2 deploy app.json prod          # 更新“生产环境”远程服务器。 Update "prod" remote server
$ pm2 deploy app.json prod revert 2 # 将“生产环境”远程服务器恢复 2。Revert "prod" remote server by 2
```

模块系统 Module system

```bash
$ pm2 module:generate [name]    # 生成名称为[name]的示例模块。Generate sample module with name [name]
$ pm2 install pm2-logrotate     # 安装模块（这里是日志循环系统）。Install module (here a log rotation system)
$ pm2 uninstall pm2-logrotate   # 卸载模块。Uninstall module
$ pm2 publish                   # 增量版本，git push 和 npm 发布。Increment version, git push and npm publish
```

#### 通过配置文件监听

```
{
  "watch": ["server", "client"], //要监视的文件
  "ignore_watch" : ["node_modules", "client/img"], //不监视的文件
  "watch_options": { 
    //监视配置
    "followSymlinks": false
  }
}
```

#### 停止监听

```
pm2 stop --watch 0
```

#### 结束进程/应用

```
pm2 stop www
pm2 stop all
```

#### 删除进程/应用

```
pm2 delete www
pm2 delete all
```

#### 查看进程/应用的资源消耗情况

```
pm2 monit
```

#### 重新启动进程/应用

```
//pm2 reset [app-name]
pm2 restart www
```

## 配置文件（控制项目）

```
// ecosystem.json
{
  "apps": [
    {
      "name": "appName", // 项目名
      "script": "start.js", // 启动脚本
      "env": {
        "COMMON_VARIABLE": "true"
      },
      "env_production": {
        "NODE_ENV": "production"
      }
    }
  ],
  "deploy": {
    "production": {
      "user": "服务器登录名",
      "host": ["服务器 IP"],
      "port": "服务器登录端口 默认 22",
      "ref": "origin/master",
      "repo": "git 仓库地址",
      "path": "/www/cl/production", // 项目路径
      "ssh_options": "StrictHostKeyChecking=no",
      "post-deploy": "yarn install && npm run build && pm2 startOrRestart ecosystem.json --env production",
      "env": {
        "NODE_ENV": "production"
      }
    },
    "plus": {
      "user": "服务器登录名",
      "host": ["服务器 IP"],
      "port": "服务器登录端口 默认 22",
      "ref": "origin/master",
      "repo": "git 仓库地址",
      "ssh_options": "StrictHostKeyChecking=no",
      "post-deploy": "yarn install && pm2 startOrRestart ecosystem.json --env production",
      "env": {
        "NODE_ENV": "production"
      }
    }
  }
}
```
