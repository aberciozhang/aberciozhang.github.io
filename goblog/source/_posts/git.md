---
title: git
tags:
  - github
  - git
  - gitee
categories: 其他
copyright: true
top: 1
toc: true
sidebar: true
date: 2021-02-06 12:15:25
---
摘要:
    本地上传,常用功能,
    gitee的使用,静态网页博客.

<!-- more -->

### git常用命令

```

```

### gitee上传本地文件

```
//在gitee上新建仓库blogcode,用于本地文件备份
https://gitee.com/abercio/blogcode.git //地址
//进入某文件cmd后git clone
git clone https://gitee.com/abercio/blogcode.git
//得到子文件夹blogcode
//进入blogcode
git init
git remote add origin https://gitee.com/abercio/blogcode.git
//准备上传到远程仓库
git add .
git commit -m "备份名"
git push -u origin master
//这种方式只能先建仓库,再拉到本地,才能上传
```

