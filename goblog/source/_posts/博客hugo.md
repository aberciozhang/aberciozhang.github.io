---
title: 博客hugo
tags:
  - hugo
  - blog
categories: 其他
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-06 21:03:57
---
摘要:
    用go语言写的博客框架,特点速度快,主题也多.上手简单,不易出错.
    人生苦短,Let's Go!
    
<!-- more -->
### 安装hugo

```
https://gohugo.io  //官方主页
https://github.com/gohugoio/hugo/releases //下载地址
//go编译方式
git clone https://github.com/gohugoio/hugo.git
//网盘,密码ttzz
https://pan.baidu.com/s/1LW1zEnVq0KStWNeFfUksXg

```

### 常用命令

```
hugo new site E:/hugo/myBlogger
hugo new title.md
git submodule add git地址
```

### 主题安装

```
git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
```

### 推送到github

```
hugo new site myblog_pub
cd myblog_pub/
git init
git submodule add https://github.com/chinanf-boy/gohugo-theme-yinwang.git themes/yinwang
git submodule init
git submodule update
 
hugo --buildDrafts
cd public/
git init
git remote rm origin 
git remote add origin https://github.com/ypbsyy/ypbsyy.github.io.git
git add -A
git commit -m "first commit"
git push -u origin master
```

