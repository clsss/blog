---
title: 'mongoDB环境搭建，服务器端'
date: '2018-06-20T20:24:11+08:00'
publishdate: '2018-06-20T20:24:11+08:00'
lastmod: '2018-06-20T20:24:11+08:00'
draft: false
tags: ['mongoDB', 'blog']
series: ['server']
categories: ['server', 'mongoDB']
# img: "images/blog/img1.jpg"
toc: true
summary: 'This is an example of adding an intro picture to the post. '
---

## 一. MongoDB 介绍

> MongoDB 是一个基于分布式文件存储的数据库。由 C++ 语言编写。旨在为 WEB 应用提供可扩展的高性能数据存储解决方案。

> MongoDB 是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。

#### 优点：

1. 高可扩展性
2. 分布式存储
3. 低成本
4. 结构灵活

## 二. 使用 mongoDB

#### 1. 安装

[安装方式](http://www.runoob.com/mongodb/mongodb-window-install.html)

```bash
# 执行后会返回安装后的路径
which mongod
```

#### 2. 启动方式：

> 安装注意事项
>
> -   需要 db 存储和日志存储文件夹
> -   添加服务，配置环境变量，启动 mongod

1. 方式 1： 打开 cmd 输入 services.msc 查看服务可以看到 MongoDB 服务
   推荐将启动方式设置为手动.等什么时候用数据库的时候再手动通过 windows 服务,或者命令行的形式进行启动.

2. 方式 2： 以管理员身份打开命令提示符,进入 C:\MongoDB\bin,然后输入下面的命令

```bash
<!--
1. dbpath：指定db存储到哪里
2. --journal：32位服务器——指定使用journal输出日志
3. 默认存储引擎不能使用：
-->
mongod -dbpath F:\mongoDB\data --journal --storageEngine=mmapv1
```

```bash
sc delete MongoDB

mongod --dbpath "F:\mongoDB\data\db" --logpath "F:\mongoDB\logs\mongodb.log" --install --serviceName "MongoDB"

net start MongoDB

```

3. 方式 3：通过配置文件启动

```bash
<!-- 新建的mongo.conf: -->
#数据库路径
dbpath=F:\mongoDB\data
#日志输出文件路径
logpath=F:\mongoDB\logs\mongodb.log
#错误日志采用追加模式，配置这个选项后mongodb的日志会追加到现有的日志文件，而不是从新创建一个新文件
logappend=true
#启用日志文件，默认启用
journal=true
#这个选项可以过滤掉一些无用的日志信息，若需要调试使用请设置为false
quiet=true
#端口号 默认为27017
port=27017
#http 配置
httpinterface=true
#指定存储引擎（win7 x32不支持，故需要配置）
storageEngine=mmapv1

<!-- 执行 -->
mongod --config f:\mongoDB\etc\mongodb.conf

<!-- 本地服务添加 -->
mongod --config f:\mongoDB\etc\mongo\mongodb.conf -- install --serviceName "MongoDB"

```

## 三. 数据库可视化工具

### 1. 安装 mongodbVUE

> 教程：https://www.cnblogs.com/xll1025/p/6443786.html

```bash
1. 启动mongo.exe
mongo
2. 命令行use（创建数据库），并要插入数据，否则无数据
user demo
# 这里不像mysql(直接insert)，而是直接的json对象操作形式【goods表】插入一条数据
db.goods.insert({id:101,"name":"mi8","salePrice":2699})
```

遇到的问题：

```bash
在网上查了之后有的人说是MongoDB版本的原因，换成2就可以了，懒得换了，继续找方法最终，切换存储引擎，MongoDB在3.2之后默认的是wiredTiger引擎，而管理软件匹配的还是之前的引擎，所以无法使用，必须重置存在引擎。
二、原因：
在2015/3/17以前，MongoDB只有一个存储引擎，叫做MMAP，MongoDB3.0的推出使得MongoDB有了两个引擎：MMAPv1和WiredTiger。
MMAPv1：适应于所有MongoDB版本，MongoDB3.0的默认引擎
WiredTiger：仅支持64位MongoDB
MongoDB两种引擎可以互相切换，我们可以在创建服务的时候指定引擎类型。
```

### 2. 安装 Studio 3T（数据库可视化工具）

> 官网：https://robomongo.org/

### 扩展

1 .建立一些软连接(就像本地的环境配置)

```bash
ln -s /mongodb/mongdb-xx-xx/bin/mongod /usr/local/bin/mongod

mongod -f(不同于windows的--config) /mongodb/etc/mongodb.conf
```

2. 存放日志等

```bash
mkdir mongodb
mv 【加压文件】mongodb
cd mongdb
mkdir data
mkdir logs
cd logs
touch mongo.log (创建日志文件)  【rm -r xxx（删除xxx文件）】
cd ..
mkdir etc
cd etc
touch mongo.conf
vi mongo.conf (编辑mongo.conf)
```

## 四. koa2 测试代码

1. app.js

```bash
// MongoDB
const mongoose = require('mongoose')
const dbConfig = require('./dbs/config.js')

// 链接数据库
mongoose.connect(dbConfig.dbs, {
  useNewUrlParser: true
})
```

2. dbs/config.js

```bash
# 数据库的配置选项
const config = {
  dbs: 'mongodb://127.0.0.1:27017/dbs'
}

module.exports = config
```

3. dbs/models/user.js

```bash
# 模型
const mongoose = require('mongoose')
// 模式
let userScheme = new mongoose.Schema({
  name: String,
  age: Number,
})

// 导出模型
module.exports = mongoose.model('User', userScheme);
```

4. router.js

```bash
# 接口
const router = require('koa-router')()
const Users = require('../dbs/models/user')

// 前缀
router.prefix('/users')

router.get('/', function (ctx, next) {
  ctx.body = 'this is a users response!'
})

// 新增用户
router.post('/addUser', async function (ctx, next) {
  let code
  const user = new Users({
    name: ctx.request.body.name,
    age: ctx.request.body.age,
  })
  try {
    await user.save()
    code = 0
  } catch (error) {
    code = -1
  }
  ctx.body = {
    code
  }
})

// 获取用户(by name)
router.post('/getUser', async function (ctx) {
  // let code
  const result = await Users.findOne({
    name: ctx.request.body.name,
  })
  const results = await Users.find({
    name: ctx.request.body.name,
  })
  // try {
  //   code = 0
  // } catch (error) {
  //   code = -1
  // }
  ctx.body = {
    code: 0,
    result,
    results,
  }
})

// 更新用户(by name)
router.post('/updateUser', async function (ctx) {
  // let code
  const result = await Users.where({
    name: ctx.request.body.name,
  }).updateOne({
    age: ctx.request.body.age,
  })

  ctx.body = {
    code: 0,
  }
})

// 删除用户(by name)
router.post('/removeUser', async function (ctx) {
  // let code
  const result = await Users.where({
    name: ctx.request.body.name,
  }).remove()

  ctx.body = {
    code: 0,
  }
})

module.exports = router
```

请求

```bash
# post
$ curl -d 'name=test' http://localhost:3000/users/removeUser
```
