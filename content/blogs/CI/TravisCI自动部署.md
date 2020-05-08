---
title: "TravisCI 自动部署 blog"
date: 2020-05-02T09:27:45+08:00
publishdate: 2020-05-02T09:27:45+08:00
lastmod: 2020-05-02T09:27:45+08:00
draft: false
img: "images/blog/img.jpg"
toc: true
summary: "Travis CI 提供的是持续集成服务（Continuous Integration，简称 CI）。它绑定 Github 上面的项目，只要有新的代码，就会自动抓取。然后，提供一个运行环境，执行测试，完成构建，还能部署到服务器。
持续集成指的是只要代码有变更，就自动运行构建和测试，反馈运行结果。确保符合预期以后，再将新代码"集成"到主干。
持续集成的好处在于，每次代码的小幅变更，就能看到运行结果，从而不断累积小的变更，而不是在开发周期结束时，一下子合并一大块代码。"
---


#### 思路

```
项目（blog分支）跟目录编写.travis.yml 
==> 提交github触发CI 
==> 打包出public静态资源文件夹 
==> 提交到master分支、同步到其他厂库master分支
```

```
// 个人访问令牌方式（coding文档查阅）
git push --quiet "https://${TOKEN_NAME}
:${TOKEN}@e.coding.net/c_illusion/hugo_blog.git" master:master
```

#### 步骤：

- 1 github中的项目（我的是hugo的blog项目）

- 2.1 coding》个人设置》访问令牌》新建令牌（备用）：
```
# 令牌的作用是为了API访问/读取（充当用户名、密码的作用）
cd4xxxxxxxxxxxxxxxxxxd16c49a
```
- 2.2 github》Settings》Developer settings 》Personal access tokens》Generate new token（备用）
```
48exxxxxxxxxxxxxxxxx0af98882
```
- 3 登录TravisCI https://www.travis-ci.org/ ，目前好像仅支持github，使用github账号登录
- 4 选中需要CI的项目进行设置（codingToken和githubToken）
 ```
Environment Variables 下添加
添加
GITHUB_TOKEN
CODING_TOKEN
```
- 5 blog项目下新建.travis.yml
```
language: node_js
node_js:
  - 10

# 指定缓存模块，可选。缓存可加快编译速度
cache:
  directories:
    - themes

# 指定仓库地址
env:
  global:
    # Github Pages
    - GITHUB_ADDR: github.com/clsss/blog.git
    # Coding Pages
    - CODING_USER: pt76t97zsh80
    - CODING_ADDR: e.coding.net/c_illusion/hugo_blog.git

# 指定dev分支触发钩子
branches:
  only:
    - blog # build dev branch only

before_install:
  - export TZ='Asia/Beijing' # 设置时区

# Start: Build Lifecycle
install:
  - wget https://github.com/gohugoio/hugo/releases/download/v0.70.0/hugo_0.70.0_Linux-64bit.tar.gz
  - tar -xzvf hugo_0.70.0_Linux-64bit.tar.gz
  - chmod +x hugo   # 给执行权限
  - export PATH=$PATH:$PWD
  - hugo version
  - rm -rf public
  - git log -p -2 | cat
  - commit_msg=$(git log -n1 --pretty=format:"%s")
  - echo 'install done!'

# 执行 hugo 生成静态页面
script:
  - hugo
  - echo 'Hugo build done !'

# 提交，邮箱；替换真实token到_config.yml文件，最后depoy部署
after_script:
  - cd ./public
  - git init
  - git config user.name "c_illusion"
  - git config user.email "670199384@qq.com"
  - git add .
  - git commit -m "自动部署：${commit_msg} "
  # Github Pages
  - git push --force --quiet "https://${GITHUB_TOKEN}@${GITHUB_ADDR}" master:master
  # Coding Pages
  - git push --force --quiet "https://c_illusion:${CODING_TOKEN}@${CODING_ADDR}" master:master
  # set tag
  - git tag v0.0.$TRAVIS_BUILD_NUMBER -a -m "tag $TRAVIS_BUILD_NUMBER"
  # Github Pages
  - git push --quiet "https://${GITHUB_TOKEN}@${GITHUB_ADDR}" master:master --tags
  # Coding Pages
  - git push --quiet "https://c_illusion:${CODING_TOKEN}@${CODING_ADDR}" master:master --tags
# End: Build LifeCycle

```


<br><br>
