---
title: git
tags:
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
//初始化
git init
git add .
git commit -m "备份名"
git remote add <name> https://...
git remote rm <name> 
git remote rename <old> <new>
git remote remove <name>
git push -f flutter master
```

密钥

```
ssh-keygen -t rsa -C "z_chunhua@126.com"
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

### gitee利用ssh上传本地文件

```
//ssh的样式
git@gitee.com:abercio/flutter.git
//Git 全局设置
git config --global user.name "abercio"
git config --global user.email "z_chunhua@126.com"
//创建 git 仓库
mkdir flutter
cd flutter
git init
touch README.md
git add README.md
git commit -m "first commit"
git remote add origin git@gitee.com:abercio/flutter.git
git push -u origin master
//已有仓库
cd existing_git_repo
git remote add origin git@gitee.com:abercio/flutter.git
git push -u origin master
//这种方式有问题!
```

flutter上传到gitee

```
//准备工作
git init//第一次
git add .
git commit -m "备份名"
//第一次,后面不需要了
git remote add flutter https://gitee.com/abercio/flutter.git
git push -f flutter master
```

blogcode上传到gitee

```
//准备工作
git init//第一次
git add .
git commit -m "备份名"
//第一次,后面不需要了
git remote add blogcode https://gitee.com/abercio/blogcode.git
git push -f blogcode master
```

