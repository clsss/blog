---
title: '第三方登录——微信登录'
date: '2017-08-25T23:12:11+08:00'
publishdate: '2017-08-25T23:54:11+08:00'
lastmod: '2017-08-25T23:54:11+08:00'
draft: false
tags: ['微信', 'web', '第三方登录']
series: ['web']
categories: ['微信']
img: 'images/blog/img.jpg'
toc: true
summary: '第三方登录'
---

## 微信登录

[微信官方文档](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419316505&token=&lang=zh_CN)

#### 一、引入微信 SDK，前端引入引入如下 JS 文件（支持 https）【微信提供】

```
http://res.wx.qq.com/connect/zh_CN/htmledition/js/wxLogin.js
```

#### 二、生成二维码(signIn.vue)

-   html:

```bash
<div id="login-wx"></div>
```

-   js:

1. 生成二维码 需要 new WxLogin({}) 实例
2. appid：微信 AppID
3. redirect_uri：扫码后重定向地址
4. href：[控制二维码样式](https://blog.csdn.net/j_bleach/article/details/77112827)

```bash
# encodeURIComponent会把 http:// 编码成  http%3A%2F%2F 而 encodeURI却不会

# 这里是使用node生成一段微信二维码样式
// eslint-disable-next-line
const WX_CODE_STYLE = 'data:text/css;base64,'
  + 'LmltcG93ZXJCb3ggLnFyY29kZSB7d2lkdGg6IDIwMHB4O30NCi5pbXBvd'
  + '2VyQm94IC50aXRsZSB7ZGlzcGxheTogbm9uZTt9DQoua'
  + 'W1wb3dlckJveCAuaW5mbyB7d2lkdGg6IDIwMHB4O30NCi5zdGF0dXNfaW'
  + 'NvbiB7ZGlzcGxheTpub25lfQ0KLmltcG93ZXJCb3ggLnN0YXR1cyB7dGV4dC1hbGlnbjogY2VudGVyO30='

const WX_APPID = 'xxxx'

getWxCode() {
# encodeURIComponent会把 http:// 编码成  http%3A%2F%2F 而 encodeURI却不会。
    /*
    这里需要注意： 1. & 必须转，2. #必须转
    const origin = window.location.origin
    const routerName = '/login/thirdParty'
    const next = encodeURIComponent(`${origin}/#${routerName}`)
    # 扫码后重定向的地址（一般为第三方登录页面）
    const redirectUri = encodeURIComponent(`http://xxx.test.com/weixin/callback?version=v2&next=${next}`)
    console.log(redirectUri)
    */

    # 核心
    var obj = new WxLogin({
      // self_redirect: true,
      id: "login-wx",
      appid: WX_APPID,
      scope: "snsapi_login",
      redirect_uri,
      state: 'v2',
      style: 'black',
      href: WX_CODE_STYLE
    });
},
```

#### 三、修改微信二维码样式：

1. node.js

```bash
const fs = require('fs');
const path = require('path')

function resolve(dir) {
  return path.join(__dirname, dir)
}

// function to encode file data to base64 encoded string
function base64Encode(file) {
  // read binary data
  console.log(resolve(file))
  const readFile = fs.readFileSync(resolve(file));
  // convert binary data to base64 encoded string
  return 'data:text/css;base64,' + Buffer.from(readFile).toString('base64');
}
console.log(base64Encode('./wx_qrcode.css'))
```

2. css

```bash
# 微信样式修改
.impowerBox .qrcode {width: 120px;border: none}
.impowerBox .title {display: none;}
.impowerBox .info {width: 200px;}
.status_icon {display:none}
.impowerBox .status {text-align: center;}
```

#### 总结一下

流程：

1. 使用微信提供的方式，生成微信二维码（包含重定向地址等）
2. 扫码跳转到重定向地址

跳转后链接(即重定向地址)：

```bash
#例如，自己写的第三方平台页面如下
ThirdParty.vue文件, path: '/login/thirdParty'
则重定向地址如下
http://localhost:8082/#/login/thirdParty?version=v2&next===http%3A%2F%2Flocalhost%3A8082%2F%23%2Flogin%2FthirdParty==&==code=061LzaAZ0lkNb0221LAZ0DtTzZ0LzaAc==&state=v2

通过链接上的code换取token，正常登录
```
