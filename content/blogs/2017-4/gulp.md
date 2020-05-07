---
title: 'Gulp工具'
date: '2017-04-25T20:54:11+08:00'
publishdate: '2017-04-25T20:54:11+08:00'
lastmod: '2017-04-25T20:54:11+08:00'
draft: false
tags: ['gulp', 'web']
series: ['web']
categories: ['Gulp']
img: 'images/blog/img.jpg'
toc: true
summary: 'This is a Gulp stream article'
---

## Gulp 是什么？

Gulp 用自动化构建工具增强你的工作流程！

-   1.易于使用: 通过代码优于配置的策略，Gulp 让简单的任务简单，复杂的任务可管理。
-   2.构建快速: 利用 Node.js 流的威力，你可以快速构建项目并减少频繁的 IO 操作。
-   3.插件高质: Gulp 严格的插件指南确保插件如你期望的那样简洁高质得工作。
-   4.易于学习: 通过最少的 API，掌握 Gulp 毫不费力，构建工作尽在掌握：如同一系列流管道。

[中文文档地址](https://www.gulpjs.com.cn/docs/).

```bash
var gulp = require('gulp');
var less = require('gulp-less');
var browserSync = require('browser-sync').create(); // 静态服务器
var reload = browserSync.reload;      // 热更新
var notify = require("gulp-notify");  // 提示
var concat = require("gulp-concat");  // 合并
var cleanCSS = require('gulp-clean-css') // css压缩
var rev = require('gulp-rev') // 版本控制
var revCollector = require('gulp-rev-collector') // 路径修改器
var runSequence = require('run-sequence') // 同步异步处理插件
var del = require('del')
var vinylPaths = require('vinyl-paths') // 管道删除
var base64 = require('gulp-base64')
var fs = require('fs')  // 文件处理模块
var imagemin = require('gulp-imagemin') // 图片压缩
var babel = require('gulp-babel')
var uglify = require('gulp-uglify')   // js压缩
var changed = require('gulp-changed') // 如果改变才编译
var rename = require('gulp-rename')   // js重命名
var rimraf = require('rimraf').sync;
var htmlmin = require('gulp-htmlmin') // js压缩

var build = {
  imgs: 'build/imgs/',
  js: 'build/js/'
}
var dev = {
  imgs: './src/imgs/',
  js: './src/js/'
}

// Erases the dist folder
gulp.task('clean', function() {
  rimraf('./build/');
});

// 删除
gulp.task('clean:all', function() {
  del(['./build/','./rev'])
});

// js编译压缩
gulp.task('js', function() {
  gulp.src(dev.js + '*.js')
    .pipe(concat('index.js'))
    .pipe(gulp.dest('./src/js'))
  gulp.src('./src/js/index.js')
    .pipe(babel({
        presets: ['env']
    }))
    .pipe(uglify())
    //.pipe(rev())  //版本迭代
    .pipe(rename('./index.min.js'))
    .pipe(gulp.dest('./build/js'))
})

// less编译
// gulp.task('less', function() {
// });
// 编译合并css
gulp.task('css', function() {
  gulp.src('./src/less/*.less')
    .pipe(less())
    .pipe(gulp.dest('./src/css'))
    .pipe(reload({stream:true}))
    // .pipe(notify('完成less==>css: [<%= file.relative %>]!'))

  gulp.src('./src/css/*.css')
    .pipe(concat('style.css'))  //合并
    .pipe(base64({
      maxImageSize: 20*1024, // 20k
      debug: true
    })) // 压缩小图
    .pipe(cleanCSS({debug: true}, function(details) {
      console.log(details.name + ': ' + details.stats.originalSize);
      console.log(details.name + ': ' + details.stats.minifiedSize);
    }))
    .pipe(rev())  // 版本控制,
    .pipe(gulp.dest('./build/css'))

    .pipe(rev.manifest()) // 生成一个rev-manifest.json的路径匹配文件
    .pipe(gulp.dest('./rev/css')) // 生成的文件放到rev

    .pipe(reload({stream:true}))  // 通知
    // .pipe(notify('完成css合并：[<%= file.relative %>]!'))
});

// imgs
gulp.task('imgs', function() {
  gulp.src('./src/imgs/*.*')
    .pipe(imagemin()) // 压缩图片
    .pipe(gulp.dest('./build/imgs'))
});

// gulp.task('temp',function(){
//   fs.exists('./rev/rev-manifest.json',function(aaa){
//     console.log(aaa)
//   })
// })

// html
gulp.task('html',function(){
  fs.exists('./rev/css/rev-manifest.json',function(val){
    if(val == true){
      gulp.src(['./rev/css/*.json','./src/index.html'])
        .pipe(changed('./src/*/*.html'))  // 监视文件是否修改
        .pipe(revCollector({
          replaceReved:true
        })) // 修正路径
        .pipe(htmlmin({
          collapseWhitespace: true, //压缩HTML
          removeComments: true,     //清除HTML注释
          minifyJS: true, //压缩页面JS
          minifyCSS: true //压缩页面CSS
        }))
        .pipe(gulp.dest('./build'))
        .pipe(reload({stream:true}))  // 通知

      gulp.src('./rev')
        .pipe(vinylPaths(del))  // 管道删除rev

    }else{
      runSequence('html') // 不建议使用同步会很慢
    }
  })

})

/**
* 静态服务器
* 这里的数组中的任务是异步执行(多线程)，但是有可能任务还没完其他任务就开始了
*/
gulp.task('default', function() {

    runSequence('clean','imgs','css','js','html') // 不建议使用同步会很慢
    browserSync.init({
        server: {
            baseDir: "./build/"
        }
    });
    gulp.watch("src/less/*.less", ['imgs','css','html']); // 监听
    gulp.watch("src/!(_)*.html", ['html']); // 监听html
    gulp.watch("src/*.js", ['js']); // 监听html
});
```
