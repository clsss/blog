---
title: 'Nuxt.js 采坑'
date: 2019-03-13T23:04:40+08:00
publishdate: 2019-03-13T23:04:40+08:00
lastmod: 2019-03-13T23:04:40+08:00
draft: false
tags: ['vue', 'Nuxt.js', 'blog']
series: ['vue']
categories: ['vue', 'Nuxt.js']
# img: "images/blog/img1.jpg"
toc: true
summary: 'This is nuxt.js mining pit'
---

## nuxt踩坑合集

[官网](https://zh.nuxtjs.org/guide/)
<br/>
[github](https://github.com/nuxt/)
<br/>
[社区](https://github.com/nuxt-community)
<br/>

### 原因分析

1. 内部原因：
    1. 调研时间不足。
        - 撸起袖子就写代码是一个错误的方式，现在回头看m站会发现很多不对的实现方式，个别模块的优化不亚于重写。
    2. 技术了解不足，科学的使用方式后知后觉。
        - 构建环境问题（不同环境相同代码，结果不同；打静态页面不可取等）。
        - 插件使用问题(sentry竟会阻塞代码，有待研究)。
        - 文档,API了解不足。
    3. 思维方式陈旧。
        - 虽然是基于vue的框架但是使用方式与vue-cli构建的应用差别甚远，用vue的那套思想会让我们走入歧途。（比如axios请求，客户端与服务端不同）
2. 外部原因:
    1. 新兴技术，自带坑；尤其是升级大版本带来的问题。
    2. 文档不全、文档更新不及时。
        - 建议使用英文版的官网，版本高于中文版。
        - 官方的文档可能是旧的。
        - 到目前为止nuxt的谷歌分析统计都是错误的实例。nuxt2.0版本之后已不适用，改用了analytics-module
    3. 插件兼容导致的问题。
        - 三方插件不支持ssr方式
        - babel-pollify编译（babel只是将一些es6，es7-8的语法转换成符合目标的js代码，但是如果我们使用一些特性或方法，比如Generator, Set, 或者一些方法。babel并不能转换为低版本浏览器识别的代码。这时就需要babel-polifill）

<br/>

### 一、 nuxt是什么？为什么要使用nuxt？

> 2016 年 10 月 25 日，zeit.co 背后的团队对外发布了 Next.js，一个 React 的服务端渲染应用框架。几小时后，与 Next.js 异曲同工，一个基于 Vue.js 的服务端渲染应用框架应运而生，我们称之为：Nuxt.js。

> Nuxt.js 是一个基于 Vue.js 的通用应用框架。

通过对客户端/服务端基础架构的抽象组织，Nuxt.js 主要关注的是应用的 UI渲染。
其核心主要是通过vue-server-renderer模块来实现服务端渲染。 vue-server-renderer是Vue服务端Node.js渲染的一个模块，用来生成HTML内容。

> 我们的目标是创建一个灵活的应用框架，你可以基于它初始化新项目的基础结构代码，或者在已有 Node.js 项目中使用 Nuxt.js。

Nuxt.js 预设了利用Vue.js开发服务端渲染的应用所需要的各种配置。
除此之外，我们还提供了一种命令叫：nuxt generate，为基于 Vue.js 的应用提供生成对应的静态站点的功能。
我们相信这个命令所提供的功能，是向开发集成各种微服务（microservices）的 Web 应用迈开的新一步。
作为框架，Nuxt.js 为 客户端/服务端 这种典型的应用架构模式提供了许多有用的特性，例如异步数据加载、中间件支持、布局支持等。

SSR意为 server-side rendering（服务端渲染）,目的是为了**解决单页面应用的SEO的问题**。

- 客户端渲染和服务器端渲染的最重要的区别是html文件的返回是在服务端还是客户端。若是在服务器端完成的，就是服务器端渲染；反之前端完成了html的拼接，则就是客户端渲染(一般为首屏渲染: vue-meta-info + prerender-spa-plugin)。

- SEO对比：[collinss.cn](collinss.cn) 和 [en.tourscool.com](en.tourscool.com)

<br/>

### 二、为什么选择nuxt

#### 1. 服务器端渲染的优缺点？

**优点：**

1. 更好的SEO 首屏加载快 由于搜索引擎爬虫抓取工具可以直接查看完全渲染的页面。
2. 首屏加载快 快速地看到完整渲染的页面，从而提高用户体验。
3. 后端生成静态化文件 即解析模板的工作完全交由Nuxt来做，客户端只要解析标准的html。

**缺点：**

1. 开发条件受限，在服务端渲染中，created和beforeCreate之外的生命周期钩子不可用；其他生命周期均为客户端。
2. 占用服务端资源，所以原则是不影响SEO数据展示的内容或者通过事件触发获取的数据还是通过客户端获取，以减轻服务端压力。
3. 学习成本相对较高
除了对webpack、Vue要熟悉，还需要掌握node、Express或者koa等相关技术。相对于客户端渲染，项目构建、部署过程更加复杂。

#### 2. 是否应该使用服务端渲染

1. 首屏加载慢
2. SEO优化

#### 3. 原理图

![image](https://user-gold-cdn.xitu.io/2019/1/22/168713bb0c0b3934?imageslim)

#### 4. 指令背后的故事

![image](https://image-static.segmentfault.com/176/176/1761766949-5878c1dfe96fa)
[更多](https://segmentfault.com/a/1190000008114613)

<br/>

### 三、使用过程中遇到的坑

#### 1. window is undefined

> 第一个“坑”就是ssr本身导致的。不管使用element-ui或者mint-ui或者其他的UI框架。都是基于window环境而非node环境。

```bash
# vue的情况下，我们是这样使用的
import Vue from 'vue'
import VueAwesomeSwiper from 'vue-awesome-swiper'
# 插件注入
Vue.use(VueAwesomeSwiper)
```

修改为支持ssr，否则就会出现window is undefined
```bash
# 做成服务端渲染需要注意引入时机
import Vue from 'vue'

# 判断环境——browser代表客户端
if (process.browser) {
  const VueAwesomeSwiper = require('vue-awesome-swiper/dist/ssr')
  Vue.use(VueAwesomeSwiper)
}

```

#### 2.【移动端】因为调用app端的方法都是使用window下的android和webkit.messageHandlers方法。但是ssr下如果直接引入appBridge.js下的方法会报错，因为服务端没有window

> 这个问题还是因为ssr本身的“坑”，也属于是框架了解不足。在服务端运行时无window下的对象。我的处理方式是在mounted生命周期下才引入appBridge.js。[官方还有处理方式是判断当前运行环境(通过process.client)](https://nuxtjs.org/faq/window-document-undefined)。【还没试过的方式，通过nuxt.config.js配置：plugins + ssr:false】

vue里面这样用的（非ssr）：
```bash
import {hideNavigationBar} from '@/assets/js/appBridge'

export default {
  // code...
}
```

ssr：
```bash
# 在需要调用app端的页面
export default {
    data() {
      return {
        isApp: this.$route.query.platform || null,
      }
    },
    mounted() {
      if (this.isApp) {
        // 引入appBridge，后面可以使用
        this.appBridge = require('@/assets/js/appBridge').default
        this.appBridge.hideNavigationBar()
      } else {
        console.log('web操作')
      }
    }
}

```
> 实际上在服务的已加是能获取到设备型号的，然后直接存store。应该是更好的方式。至于引用appBridge的方式暂时没有想到好方法。
可以使用process.client判断是客户端

#### 3. [Vue warn]: The client-side rendered virtual DOM tree is not matching server-rendered content. This is likely caused by incorrect HTML markup, for example nesting block-level elements inside<p>, or missing <tbody>. Bailing hydration and performing full client-side render.

> 服务端与客户端渲染的DOM结构不一样，HTML结构嵌套不正确，但大多数情况下并不是嵌套结构的问题，而是因为==服务端与客户端渲染的DOM结构不一样==

1. 错误嵌套代码示例：
```bash
# 错误原因<p><div></div></p> 或 <span><p></p></span>
<p class="text">
    <van-checkbox class="tour-checkbox"
      v-model="checked">
      <span>我已经阅读并同意《服务协议》</span>
    </van-checkbox>
</p>
```
修正：
```bash
<div class="text">
    <van-checkbox class="tour-checkbox"
      v-model="checked">
      <span>我已经阅读并同意《服务协议》</span>
    </van-checkbox>
</div>
```

2. 服务端与客户端渲染的DOM结构不一致

```bash
# nuxt.config.js配置
plugins: [
  {
    src: '~/plugins/mint-ui',
    ssr: true // 划重点！！！设为true后，ssr会进行初始化时前后dom比较，保证初始化前后一致
  }, {
    src: "~/plugins/vue-swiper",
    ssr: false // 禁用服务端渲染
  }, 
]
```

这边以 [vue-awesome-swiper](https://github.com/surmon-china/vue-awesome-swiper) 为例，官网也给出了明确的ssr使用方式，请仔细阅读，一定要用ssr的方式！

> 例子中的类名.swiper-wrapper类名千万不要修改。


#### 4. 【移动端】postcss-px2rem会导致其他本身已支持自适应的UI库（vant UI）样式缩小。

>解决方式: postcss-px2rem-exclude代替postcss-px2rem，配置如下

```bash
# nuxt.config.js
build:{
    postcss: [
      require('postcss-px2rem-exclude')({
        remUnit: 75, // 转换基本单位
        exclude: /vant/i
      }),
      require('autoprefixer')({
        browsers: ['last 3 versions']
      })
    ],
}
```

<br/>

#### 5. this.$route.params一直为空，但query是正常。郁闷了我一个小时。

> 这个坑我不想说什么了。可能是用vue就习惯了用path跳转，但在nuxt中可能要换成使用name了，因为用params传参这里就必须带上name，但为什么必须带name我不清楚【或许是nuxt中的pages就是遍历出来的路由所以用了pages下的文件名作为name,而自定义的name是失效的】。

> 有兴趣可以试试：自定义name不生效，而是使用创建好的pages下的页面名,比如demo/test.vue(name为'demo-test')

错误代码：

```bash
this.$router.push({
  path: '/demo/test', // demo-test
  params: {
    type: this.type
  }
})
```

正确代码:

```bash
this.$router.push({
  name: 'demo-test', // 划重点!!!,params须用name!（pages/demo/test.vue）
  path: '/demo/test',
  params: {
    type: this.type
  }
})
```

#### 6. vuex store (nuxt.js 提供了2种store modes)

> Modules: every .js file inside the store directory is transformed as a namespaced module (index being the root module).（不需要new Vuex.Store）

> Classic (deprecated): store/index.js returns a method to create a store instance. 这个2.4x版本已经提示要作废了。nuxt3将不存在此用法

[官方示例](https://github.com/nuxt/nuxt.js/blob/dev/examples/vuex-store/store)

我的store目录结构（使用Modules mode）：

```bash
├─state.js
├─actions.js
├─getters.js
├─mutations.js
├─login(login模块)
   ├─index.js

```

```bash
// state.js 必须导出返回对象的方法，创建独立实例
export default () => ({
  profile: {},
})

// getters.js，actions.js，mutations.js都是直接导出对象
export default {
    // 方法
}

```

```bash
// login/index.js
export const state = () => ({
  loginData: {
    time: new Date()
  }
})

export const actions = {}

export const mutations = {}
```

#### 7. 工厂模式api

> issue中有人提出了[https://github.com/nuxt-community/axios-module/pull/102](https://github.com/nuxt-community/axios-module/pull/102)

```bash
# 使用axios module
let apiConfig = require('~/apiConf.env')

export default function ({
  $axios,
  redirect
}) {
  // 设置基础配置
  $axios.defaults.timeout = 10000
  $axios.defaults.withCredentials = true
  $axios.defaults.headers = {
    'Content-Type': 'application/json; charset=utf-8', // json格式通信
    'platform': 'pc',
  }
  if (process.env.NODE_ENV === 'production') {
    $axios.defaults.baseURL = apiConfig.base
  }
  // post请求头设置
  // $axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded;charset=UTF-8';

  // 请求回调
  $axios.onRequest(config => {
    console.log('Making request to ' + config.url)
    return config
  })

  // 响应回调
  $axios.onResponse(res => {
    console.log('Making response to ' + res.url)
    return res.data
  })

  // 错误回调
  $axios.onError(error => {
    const code = parseInt(error.response && error.response.status)
    if (code === 400) {
      console.log('400')
    }
  })
}

```

#### 8. yarn generate 打静态包问题

> 如果你想使用nuxt generate打静态文件的包，那么狠遗憾，动态路由无法静态打包；并且你将失去服务端渲染的一切优势。asyncData等服务端能用的方式，都不能再使用。那你为什么还要用静态打包.但是如果你的项目只是静态页面的话，做静态化部署是完全OK 的。但如果牵扯到请求，还是服务器端的部署吧

官网所给的方式很气人: https://zh.nuxtjs.org/api/configuration-generate

```bash
const axios = require('axios')

module.exports = {
  generate: {
    routes: function () {
      return axios.get('https://my-api/users')
      .then((res) => {
        return res.data.map((user) => {
          return '/users/' + user.id
        })
      })
    }
  }
}
// 实际上也是通过axios.get('https://my-api/users')请求，返回所有id的数组,类似：[{id: 1}, {id: 2}...]；
// 数据量大的话自己想象。不喜欢query传参，但目前我个人觉得打包成静态的文件的话，还是使用query是最好的方式了。
```

动态路由静态打包：
在nuxt.js的issues找到了这个：[https://github.com/nuxt/nuxt.js/issues/73](https://github.com/nuxt/nuxt.js/issues/73) 可以参考

<br><br>

#### 构建

1. core-js问题

```bash
https://github.com/nuxt/nuxt.js/pull/5317
https://github.com/nuxt/nuxt.js/pull/5411
解决方式：https://github.com/nuxt/nuxt.js/pull/5352
```

2. less3.0bug，iview主题色修改遇到

```bash
// https://github.com/ant-design/ant-motion/issues/44
extend(config, ctx) {
  config.module.rules.push({
    test: /\.less$/,
    loader: 'less-loader',
    options: {
      javascriptEnabled: true, 
    }
  })
}
```

3. extractCSS与parallel不可并行;IE或者Edge下报错原因

```bash
// analyze: true,
// extractCSS与parallel不可并行：https://github.com/nuxt/nuxt.js/pull/5004
extractCSS: (process.env.NODE_ENV === 'production'), // 拆分css
maxChunkSize: 30000,
// parallel: true, // 多进程
// IE或者Edge下报错原因：（https://github.com/Rich-Harris/devalue/issues/16）
// 处理
// https://github.com/nuxt/nuxt.js/issues/4432
// https://github.com/nuxt/nuxt.js/issues/4643
// https://github.com/nuxt/nuxt.js/pull/4600
```
