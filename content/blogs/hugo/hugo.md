---
title: "Hugo 搭建博客"
date: "2017-10-25T20:54:11+08:00"
publishdate: "2017-10-25T20:54:11+08:00"
lastmod: "2017-10-25T20:54:11+08:00"
draft: false
tags: ["Hugo", "blog", "web"]
series: ["Hugo"]
categories: ["Hugo"]
img: "images/blog/hugo-logo.png"
toc: true
summary: "This was Hugo's attempt "
---

<br/>
## 基于 Hogo 快速搭建静态网站

> 本来使用 Hexo, 也是极好的，素材也很多。但是文章一旦多了。慢速度又怎么让人心安，所以使用 Hugo 作一次尝试。一起来看看如何使用 hugo 搭建自己 blog.
附上[中文网](http://www.gohugo.org)  
<br/>

<br/>
### 安装 Hugo

工欲兴其事必先利器。

- 安装 Git
- 安装 Go
- 安装 Mercurial
- 安装 Hugo [具体步骤官网很详细](http://www.gohugo.org/doc/overview/installing/)

---
* 使用 Windows 的小伙伴就有点难受了。由于地址是国外的，所以没有翻墙的小伙伴，下载靠运气

> 请确保安装好后添加全局路径

<br/>
### 在本地创建自己的站点

在你想创建站点的地方下执行

```bash
$ hugo new site [文件夹]
# 进入创建的站点
$ cd [文件夹]
```

<br/>
### 新建一个页面/文章

在进入的站点根目录下执行

```bash
$ hugo new page1.md
```

新创建的文件在目录 content/ 里:

```bash
+++
date = "2018-10-08T08:36:54-07:00"
draft = true
title = "page1"

+++

# 编辑自己.md文件吧~~~
```

<br/>
### 本地运行

```bash
$ hugo server --theme=hyde --buildDrafts
```

两个参数：

- theme 使用的皮肤——hyde;
- buildDrafts 显示我们的内容，包括设置了 draft: false（非草稿状态）/ true（草稿状态）的内容。

> 访问：http://localhost:1313/blog/
（因为我的github地址是https://clsss.github.io/blog/）。
> 如果draft: true,则有可能生成的页面提交到github后访问页面是空白页面

<br/>
### 部署

1. 在 github 创建仓库并复制地址

2. 在根目录下执行命令，所有静态页面都会生成到 public 目录

```bash
$ hugo --theme=hyde --baseUrl="【你的github仓库地址】"
```

如果将 theme，baseUrl 写入了 config.toml
则直接运行

```bash
$ hugo
```

<br/>
### 提交到 github

这里只需要上传public下的静态资源，所以进入public
```bash
$ cd public
$ git init
$ git remote add origin 【你的github地址】
$ git add -A
$ git commit -m "描述"
$ git push -u origin master
```
