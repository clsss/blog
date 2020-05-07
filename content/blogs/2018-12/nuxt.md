---
title: 'Nuxt.js'
date: '2018-12-23T20:24:11+08:00'
publishdate: '2018-12-23T20:24:11+08:00'
lastmod: '2018-12-23T20:24:11+08:00'
draft: false
tags: ['vue', 'Nuxt.js', 'blog']
series: ['vue']
categories: ['vue', 'Nuxt.js']
# img: "images/blog/img1.jpg"
toc: true
summary: 'This is nuxt.js'
---

## 一. Nuxt.js 介绍

> vue + vuex + vue-router + SSR + vue-meta
> 创建即配置。方便

[官网](https://zh.nuxtjs.org/guide)

> Nuxt.js 是一个基于 Vue.js 的通用应用框架。
> 通过对客户端/服务端基础架构的抽象组织，Nuxt.js 主要关注的是应用的 UI 渲染。
> 我们的目标是创建一个灵活的应用框架，你可以基于它初始化新项目的基础结构代码，或者在已有 Node.js 项目中使用 Nuxt.js。

#### 特性

1. 基于 Vue.js
2. 自动代码分层
3. 服务端渲染
4. 强大的路由功能，支持异步数据
5. 静态文件服务
6. ES6/ES7 语法支持
7. 打包和压缩 JS 和 CSS
8. HTML 头部标签管理
9. 本地开发支持热加载
10. 集成 ESLint
11. 支持各种样式预处理器： SASS、LESS、 Stylus 等等
12. 支持 HTTP/2 推送

#### 服务端渲染

> 当运行 nuxt 命令时，会启动一个支持 热加载 和 服务端渲染（基于 Vue.js 的 vue-server-renderer 模块）的开发服务器。

#### 生命周期

1. incoming Request
2. nuxtServerlint（store action）
3. middleware
4. validata()
5. asyncData() 【获取组件数据】/ fetch() 【获取数据 store】
6. render() ==> middleware

## 二. 使用 Nuxt.js

#### 1. 安装

```bash
$ npx create-nuxt-app <项目名>
或
$ yarn create nuxt-app <项目名>
```

#### 2. 启动方式：

```bash
cd <项目名>
yarn run dev
```

#### 3. 添加 babel

> node 不支持 import/export 等 es6 语法

1. 如果没有安装 babel-cli，请先安装

```bash
# 因为babel-node是babel-cli工具自带的一个命令
yarn add babel-cli
```

2. 修改 package.json

```bash
# 使用babel启动脚本
添加 --exec babel-node 修改为：
"dev": "cross-env NODE_ENV=development nodemon server/index.js --watch server --exec babel-node",
```

3. yarn add babel-preset-2015

4. 新增.babelrc 文件

```bash
{
  "presets": ["es2015"]
}
```

5. 对 scss 文件的转译

```bash
yarn add sass-loader node-sass
```

#### 4. 目录讲解

```bash
目录
├── assets（资源目录：用于组织未编译的静态资源如 LESS、SASS 或 JavaScript）
├── components（组件目录： 用于组织应用的 Vue.js 组件。Nuxt.js 不会扩展增强该目录下 Vue.js 组件，即这些组件不会像页面组件那样有 asyncData 方法的特性。）
├── layouts（布局目录：用于组织应用的布局组件。）
├── middleware（中间件目录：目录用于存放应用的中间件。）
├── node_modules
├── pages（页面目录：用于组织应用的路由及视图。Nuxt.js 框架读取该目录下所有的 .vue 文件并自动生成对应的路由配置。）
├── plugins（插件目录：用于组织那些需要在 根vue.js应用 实例化之前需要运行的 Javascript 插件。）
├── server
├── static（静态文件目录：用于存放应用的静态文件，此类文件不会被 Nuxt.js 调用 Webpack 进行构建编译处理。 服务器启动的时候，该目录下的文件会映射至应用的根路径 / 下）
├── store（该目录用于组织应用的 Vuex 状态树 文件。 Nuxt.js 框架集成了 Vuex 状态树 的相关功能配置，在 store 目录下创建一个 index.js 文件可激活这些配置。）
├── static
├── .editorconfig
├── .gitignore
├── .eslintrc
├── nuxt.config.js（ 文件用于组织Nuxt.js 应用的个性化配置，以便覆盖默认配置。该文件名为Nuxt.js保留的，不可更改。）
├── package.json
└── README.md
```

#### 5. 路由

> pages 下新建一个.vue 文件就会创建一个对应的路由 创建即配置

#### 6. 页面模板（layouts）

> 公共的模板（头部，尾部处理）可以自定义。
> default.vue 如果没有配置过，则默认是 default.vue

#### 7. 异步数据，SSR 讲解

server:

```bash
ctx.body = {
  code: 0,
  msg: '',
  list: [{name: zhangsan}, {name: lisi}]
}
```

client:

```bash
data() {
  return {
    list
  }
},
// 在服务端render之前获取数据
async asyncData() {
  let {status, data: {list}} = await axios.get('http://localhost:3001/list')
  if (status === 200) {
    return {
      list
    }
  }
}
```

> 1. 服务端下发的是模板和数据组合好的数据
> 2. 除了下发模板，也下发了数据，通过注入全局返回了请求的数据

```bash
window.__NUXT__={layout: xx, data: {list: [{name: zhangsan}, {name: lisi}]}}
```

#### 8. vuex 的应用

用法与正常的 vuex 一样。
index.js

```bash
import Vue from 'vue'
import Vuex from 'vuex'
import module1 from './modules/module1.js'

Vue.use(Vuex)

const store = () => new Vuex.Store({
  modules: {
    module1
  },
  state: {},
  mutations: {},
  actions: {},
})

export default store
```

module1.js

```bash
const state = () => ({
  list: ['11', '22']
})

const mutations = {
  add(state, data) {
    state.list.push(data)
  }
}

const actions = {
  add({
    commit
  }, data) {
    commit('add', data)
  }
}

export default {
  namespaced: true,
  state,
  mutations,
  actions,
}

```
