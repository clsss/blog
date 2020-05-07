---
title: "Node与Js的区别"
date: 2019-06-04T20:17:53+08:00
publishdate: 2019-06-04T20:17:53+08:00
lastmod: 2019-06-04T20:17:53+08:00
draft: false
img: ""
toc: true
summary: "Node与Js的区别"
---

> 难点：不同环境下的问题，学习一门语言可能只要几天，但熟悉一门语言的时间可能要1-2年

### 一、Node与Js的区别

- ECMAScript（语法）
    - 定义变量，循环，判断，函数
    - 原型链，作用域和闭包，异步
    - 不能操DOM，不能监听事件，不能发送ajax
    - 不能处理http请求，不能操作文件
- javascript
    - 使用ES规范 + [web API](https://developer.mozilla.org/zh-CN/docs/Web/API) (DOM,BOM,事件,Ajax)
- Node.js
    - 使用ES规范 + [Node.js API](http://nodejs.cn/api/) (http,文件等)

#### 1. 服务稳定性

1. server端可能会遭受各种攻击和误操作，接口是暴露在整个互联网
2. 单个客户端可以挂，但服务端不能挂
3. PM2进程守候（挂了重启）

#### 2. 考虑内存和CPU（优化，扩展）

1. 客户端独占一个浏览器，内存和CPU都不是问题，不会造成问题；
2. server端要承载很多请求，CPU和内存资源稀缺
3. steam写日志，redis存session

#### 3. 日志记录

1. 前端只会发起日志，并不关系后续分析
2  服务端需要记录日志，存储日志，分析日志

#### 4. 安全

1. 前端攻击
  XSS（跨站脚本Cross-site scripting），
  CSRF（跨站请求伪造Cross-site request forgery）
2. 越权操作，数据库攻击等
3. 登录验证，预防xss和sql注入

#### 5. 集群和服务拆分

1. 产品发展速度快，流量迅速增加
2. 扩展机器或者服务拆分来承载大流量

### 二、搭建

1. nodemon 监听文件变化，自动启动node
2. cross-env 设置环境变量，兼容mac linux和windows

```bash
# package.json
{
  "name": "node",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "dev": "cross-env NODE_ENV=dev nodemon ./bin/www.js",
    "build": "cross-env NODE_ENV=product node ./bin/www.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "dependencies": {
    "cross-env": "^5.2.0",
    "mysql": "^2.17.1",
    "nodemon": "^1.19.0",
    "redis": "^2.8.0"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}

```

启动server服务

```bash
# bin/www.js
const http = require('http')

const POST = 8000
const serverHandle = require('../index.js')

const server = http.createServer(serverHandle)
server.listen(POST)
console.log(`server linsening at http://127.0.0.1:${POST}`)
```

封装serverHandle方法

```bash
const serverHandle = (req, res) => {
  // 记录log
  access(`${req.method} -- ${req.url} -- ${req.headers['user-agent']} -- ${Date.now()}`)

  // 设置返回的格式为 JSON
  res.setHeader('Content-type', 'application/json')

  // 获取path
  const url = req.url
  req.path = url.split('?')[0]

  // 解析参数
  req.query = querystirng.parse(url.split('?')[1])

  // 解析cookie
  req.cookie = {}
  const cookieStr = req.headers.cookie || '' // key1=val1;key2=val2
  cookieStr.split(';').forEach(item => {
    if (!item) { return }
    const arr = item.split('=')
    const key = arr[0].trim()
    const val = arr[1].trim()
    req.cookie[key] = val
  });

  // 解析 session （使用 redis）
  let needSetCookie = false
  let userId = req.cookie.userid
  if (!userId) {
    needSetCookie = true
    userId = `${Date.now()}_${Math.random()}`
    // 初始化 redis 中的 session 值
    set(userId, {})
  }

  // 获取 session
  req.sessionId = userId
  get(req.sessionId).then(sessionData => {
    if (sessionData == null) {
      // 初始化 redis 中的 session 值
      set(req.sessionId, {})
      // 设置 session
      req.session = {}
    } else {
      // 设置 session
      req.session = sessionData
    }
  
    // 处理 post data
    return getPostData(req)
  }).then(postData => {
    req.body = postData

    const productResult = handleProductRouter(req, res)
    if (productResult) {
      // code...
      return
    }

    const userResult = handleUserRouter(req, res)
    if (userResult) {
      // code...
      return
    }

    // 未命中路由，返回 404
    res.writeHead(404, { "Content-type": "text/plain" })
    res.write("404 Not Found\n")
    res.end()
  })
}

module.exports = serverHandle
```
