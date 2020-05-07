---
title: 'Redis'
date: '2018-12-23T20:24:11+08:00'
publishdate: '2018-12-23T20:24:11+08:00'
lastmod: '2018-12-23T20:24:11+08:00'
draft: false
tags: ['redis', 'blog']
series: ['server']
categories: ['server', 'redis']
# img: "images/blog/img1.jpg"
toc: true
summary: 'This is redis.'
---

## 一. Redis 介绍

> Redis 是一个开源的使用 ANSI C 语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value 数据库，并提供多种语言的 API。从 2010 年 3 月 15 日起，Redis 的开发工作由 VMware 主持。从 2013 年 5 月开始，Redis 的开发由 Pivotal 赞助。
> 快速读写

#### 性能：

-   测试完成了 50 个并发执行 100000 个请求。
-   设置和获取的值是一个 256 字节字符串。
-   Linux box 是运行 Linux 2.6,这是 X3320 Xeon 2.5 ghz。
-   文本执行使用 loopback 接口(127.0.0.1)。

结果:读的速度是 110000 次/s,写的速度是 81000 次/s 。

## 二. 使用 Redis

#### 1. 安装

[安装方式](https://github.com/MicrosoftArchive/redis/releases)

#### 2. 启动方式：

正常启动

```bash
redis-server
```

windows 下安装 Redis 第一次启动报错：
[2368] 21 Apr 02:57:05.611 # Creating Server TCP listening socket 127.0.0.1:6379: bind: No error
解决方法：在命令行中运行

```bash
redis-cli.exe
127.0.0.1:6379>shutdown
not connected>exit
然后重新运行
redis-server.exe redis.windows.conf
启动成功！
```

#### 3. 连接 redis 数据库

这里我使用的是 koa 所以依赖于中间键

-   1. koa-redis
-   2. koa-generic-session

代码演示：

```bash
// redis
const session = require('koa-generic-session')
const Redis = require('koa-redis')

// 链接redis，keys用于session加密，使用redis数据库存储sessions，可以有效避免session导致的内存不够的情况
app.keys=['key', 'keyskeys']
app.use(session({
  key: 'sk',
  prefix: 'skpr',
  store: new Redis()
}))
```
