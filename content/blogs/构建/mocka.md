---
title: '测试&持续构建'
date: '2018-04-25T20:54:11+08:00'
publishdate: '2018-04-25T20:54:11+08:00'
lastmod: '2018-04-25T20:54:11+08:00'
draft: false
tags: ['mocha', 'web']
series: ['mocha']
categories: ['test']
# img: "images/banner/test1.jpg"
toc: true
summary: 'mocha test'
---

## 测试&持续构建

#### 一. api 测试

1. 频繁地将代码集成到主干
2. 每次集成都通过自动化的构建来验证

-   优点：
-   1. 尽早发现错误
-   2. 防止分支大幅度偏离主干

-   工具
    egg—— https://github.com/eggjs/egg
    持续构建： https://travis-ci.org/eggjs/egg
    文档： https://docs.travis-ci.com/

benchmark 性能测试
benchmark.js
suite

```bash
.add('parseInt()', () => {
	num1('123465');
})
.add('Number', () => {
	num2('123456');
})
.on('cycle', event => {
	console.log(String(event.target));
})
.on('complete', function() {
	console.log('Fastest is ' + this.filter('fastest').map('name'));
})
.run({async: true});
```

#### 二. UI 测试

jest

三 爬虫
爬虫:
按照一定的需求自动抓取网络信息
反爬虫:

1. user-agent，referer, 验证码
2. 单位时间访问次数
3. 关键信息用图片混淆
4. 异步加载

使用：suppeteer+cheerio

through2,
express, koa, egg
SSP & 同构
NodeJS 源码
