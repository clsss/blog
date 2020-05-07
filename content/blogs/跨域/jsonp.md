---
title: 'jsonp跨域原理'
date: '2018-08-25T23:12:11+08:00'
publishdate: '2018-08-25T23:54:11+08:00'
lastmod: '2018-08-25T23:54:11+08:00'
draft: false
tags: ['跨域', 'web']
series: ['web']
categories: ['跨域']
img: 'images/blog/img.jpg'
toc: true
summary: 'This is a cross domain'
---

## 一. 跨域:

> 浏览器对于 javascript 的同源策略的限制,例如 a.cn 下面的 js 不能调用 b.cn 中的 js,对象或数据(因为 a.cn 和 b.cn 是不同域),所以跨域就出现了

同源策略:
请求的 url 地址,必须与浏览器上的 url 地址处于同域上,也就是域名,端口,协议相同.
比如:我在本地上的域名是 study.cn,请求另外一个域名一段数据

JSONP 的优点是：它不像 XMLHttpRequest 对象实现的 Ajax 请求那样受到同源策略的限制；它的兼容性更好，在更加古老的浏览器中都可以运行，不需要 XMLHttpRequest 或 ActiveX 的支持；并且在请求完毕后可以通过调用 callback 的方式回传结果。

JSONP 的缺点则是：它只支持 GET 请求而不支持 POST 等其它类型的 HTTP 请求；它只支持跨域 HTTP 请求这种情况，不能解决不同域的两个页面之间如何进行 JavaScript 调用的问题。

### 1.1 jsonp

例子

```bash
# page1：
<script type="text/javascript">
    var message = function(data) {
      alert(data[1].title);
    };
</script>
<script type="text/javascript" src="http://web.cn/js/message.js"></script>
# message.js
message([
    {"id":"1", "title":"aaa"},
    {"id":"2", "title":"bbb"},
]);
```
