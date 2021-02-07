---
title: 博客hexo
date: 2021-02-05 14:46:57
tags:
    - hexo
    - blog
categories: 其他
top: 90
toc: true # 是否启用内容索引
sidebar: true # 是否启用sidebar侧边栏，none：不启用
---
摘要:
    go语言最常用的微服务框架
    微服务模块化使用
<!-- more -->
### hexo博客框架

### 安装nodejs

```
http://nodejs.cn/
```

### 本地

```
npm config set registry https://registry.npm.taobao.org
```

### 安装cnpm

```
npm install cnpm -g	//两条选其中一个
npm install hexo-cli -g 
```

### 安装hexo

```
https://hexo.io/zh-cn/
cnpm install hexo -g

```

### 安装博客

```
hexo init myblog  //在当前目录下生成myblog子目录
npm install
```

### 目录安装git上传插件

```
npm install hexo-deployer-git --save
```

### 配置_config.yml

```
# Deploymentconfig.yml
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: git
  repo: https://gitee.com/abercio/abercio.git
  branch: master
```

### 更换主题

```
进入themes目录或者git后面加themes/主题名目录
git clone https://github.com/HmyBmny/hexo-theme-concise.git themes/concise
在_config.yml中
theme: hexo-theme-xups
```

### 主题next安装

```
cd hexo-site
npm install hexo-theme-next
//或者直接clone
cd hexo-site
git clone https://github.com/next-theme/hexo-theme-next themes/next
```

### 主题next更换模板

```
# Schemes
scheme: Muse
#scheme: Mist
#scheme: Pisces
#scheme: Gemini
```

### 主题next右上角实现fork

```
//themes/next/layout/_layout.swig
//新版本themes/next/layout/_layout.njk
//放在<div class="headband"></div>的下面)，并把href改为你的github地址
//样式代码地址https://tholman.com/github-corners/

```

```
//样式1,蓝底右上角
<a href="https://your-url" class="github-corner" aria-label="View source on GitHub"><svg width="80" height="80" viewBox="0 0 250 250" style="fill:#70B7FD; color:#fff; position: absolute; top: 0; border: 0; right: 0;" aria-hidden="true"><path d="M0,0 L115,115 L130,115 L142,142 L250,250 L250,0 Z"></path><path d="M128.3,109.0 C113.8,99.7 119.0,89.6 119.0,89.6 C122.0,82.7 120.5,78.6 120.5,78.6 C119.2,72.0 123.4,76.3 123.4,76.3 C127.3,80.9 125.5,87.3 125.5,87.3 C122.9,97.6 130.6,101.9 134.4,103.2" fill="currentColor" style="transform-origin: 130px 106px;" class="octo-arm"></path><path d="M115.0,115.0 C114.9,115.1 118.7,116.5 119.8,115.4 L133.7,101.6 C136.9,99.2 139.9,98.4 142.2,98.6 C133.8,88.0 127.5,74.4 143.8,58.0 C148.5,53.4 154.0,51.2 159.7,51.0 C160.3,49.4 163.2,43.6 171.4,40.1 C171.4,40.1 176.1,42.5 178.8,56.2 C183.1,58.6 187.2,61.8 190.9,65.4 C194.5,69.0 197.7,73.2 200.1,77.6 C213.8,80.2 216.3,84.9 216.3,84.9 C212.7,93.1 206.9,96.0 205.4,96.6 C205.1,102.4 203.0,107.8 198.3,112.5 C181.9,128.9 168.3,122.5 157.7,114.1 C157.9,116.9 156.7,120.9 152.7,124.9 L141.0,136.5 C139.8,137.7 141.6,141.9 141.8,141.8 Z" fill="currentColor" class="octo-body"></path></svg></a><style>.github-corner:hover .octo-arm{animation:octocat-wave 560ms ease-in-out}@keyframes octocat-wave{0%,100%{transform:rotate(0)}20%,60%{transform:rotate(-25deg)}40%,80%{transform:rotate(10deg)}}@media (max-width:500px){.github-corner:hover .octo-arm{animation:none}.github-corner .octo-arm{animation:octocat-wave 560ms ease-in-out}}</style>
```

```
//样式2,红底左上角
<a href="https://your-url" class="github-corner" aria-label="View source on GitHub"><svg width="80" height="80" viewBox="0 0 250 250" style="fill:#FD6C6C; color:#fff; position: absolute; top: 0; border: 0; left: 0; transform: scale(-1, 1);" aria-hidden="true"><path d="M0,0 L115,115 L130,115 L142,142 L250,250 L250,0 Z"></path><path d="M128.3,109.0 C113.8,99.7 119.0,89.6 119.0,89.6 C122.0,82.7 120.5,78.6 120.5,78.6 C119.2,72.0 123.4,76.3 123.4,76.3 C127.3,80.9 125.5,87.3 125.5,87.3 C122.9,97.6 130.6,101.9 134.4,103.2" fill="currentColor" style="transform-origin: 130px 106px;" class="octo-arm"></path><path d="M115.0,115.0 C114.9,115.1 118.7,116.5 119.8,115.4 L133.7,101.6 C136.9,99.2 139.9,98.4 142.2,98.6 C133.8,88.0 127.5,74.4 143.8,58.0 C148.5,53.4 154.0,51.2 159.7,51.0 C160.3,49.4 163.2,43.6 171.4,40.1 C171.4,40.1 176.1,42.5 178.8,56.2 C183.1,58.6 187.2,61.8 190.9,65.4 C194.5,69.0 197.7,73.2 200.1,77.6 C213.8,80.2 216.3,84.9 216.3,84.9 C212.7,93.1 206.9,96.0 205.4,96.6 C205.1,102.4 203.0,107.8 198.3,112.5 C181.9,128.9 168.3,122.5 157.7,114.1 C157.9,116.9 156.7,120.9 152.7,124.9 L141.0,136.5 C139.8,137.7 141.6,141.9 141.8,141.8 Z" fill="currentColor" class="octo-body"></path></svg></a><style>.github-corner:hover .octo-arm{animation:octocat-wave 560ms ease-in-out}@keyframes octocat-wave{0%,100%{transform:rotate(0)}20%,60%{transform:rotate(-25deg)}40%,80%{transform:rotate(10deg)}}@media (max-width:500px){.github-corner:hover .octo-arm{animation:none}.github-corner .octo-arm{animation:octocat-wave 560ms ease-in-out}}</style>
```

### 主题next添加RSS

```
//在博客框架的根目录上安装插件
 npm install --save hexo-generator-feed
 //再打开根目录的_config.yml的末尾
# Extensions
## Plugins: http://hexo.io/plugins/
plugins: hexo-generate-feed

```

```
//打开next主题文件夹里面的_config.yml,在里面配置为如下样子：(就是在rss:的后面加上/atom.xml
# Set rss to false to disable feed link.
# Leave rss as empty to use site's feed link.
# Set rss to specific value if you have burned your feed already.
rss: /atom.xml

```

### 主题next设置背景

```
//再next的配置文件中,false改为true有背景
canvas_ribbon:
  enable: true
  size: 300 # The width of the ribbon
  alpha: 0.6 # The transparency of the ribbon
  zIndex: -1 # The display level of the ribbon
```

### 主题next加载进度条

```
//再next的配置文件中,false改为true有背景
# Progress bar in the top during page loading.
# For more information: https://github.com/rstacruz/nprogress
nprogress:
  enable: true
  spinner: true
  
```

### 主题next动态背景

```
//如果next主题在5.1.1以上的话就不用我这样设置，直接在主题配置文件中找到canvas_nest: false，把它改为canvas_nest: true就行
//另一种方式,修改next主题下layout/_layout.njk
//在 < /body>之前添加代码(不要放在< /head>的后面)
{% if theme.canvas_nest %}
<script type="text/javascript" src="//cdn.bootcss.com/canvas-nest.js/1.0.0/canvas-nest.min.js"></script>
{% endif %}
//修改主题配置文件/next/_config.yml
//在里面添加如下代码：(可以放在最后面)
# ---------------------------------------------------------
# background settings
# ---------------------------------------------------------
# add canvas-nest effect
# see detail from https://github.com/hustcc/canvas-nest.js
canvas_nest: true
```

```
//如果你感觉默认的线条太多的话
//在上一步修改 _layout.swig中替换以下
{% if theme.canvas_nest %}
<script type="text/javascript"
color="0,0,255" opacity='0.7' zIndex="-2" count="99" src="//cdn.bootcss.com/canvas-nest.js/1.0.0/canvas-nest.min.js"></script>
{% endif %}
//color ：线条颜色, 默认: '0,0,0'；三个数字分别为(R,G,B)
//opacity: 线条透明度（0~1）, 默认: 0.5
//count: 线条的总数量, 默认: 150
//zIndex: 背景的z-index属性，css属性用于控制所在层的位置, 默认: -1
```

### 主题next桃心效果

```
//http://7u2ss1.com1.z0.glb.clouddn.com/love.js
//下载
```



### 主题next修改内链接样式

```
//themes\next\source\css\_common\components\post\post.styl，
//在末尾添加如下css样式
// 文章内链接文本样式
.post-body p a{
  color: #0593d3;
  border-bottom: none;
  border-bottom: 1px solid #0593d3;
  &:hover {
    color: #fc6423;
    border-bottom: none;
    border-bottom: 1px solid #fc6423;
  }
}
```

### 主题next改文章底部带#标签

```
//修改模板/themes/next/layout/_macro/post.swig，
//搜索 rel="tag">#，
//将 # 换成<i class="fa fa-tag"></i>
```



### 主题next文章尾添结束

```
//在路径 \themes\next\layout\_macro 中
//新建 passage-end-tag.swig 文件,并添加以下内容
<div>
    {% if not is_index %}
        <div style="text-align:center;color: #ccc;font-size:14px;">-------------本文结束<i class="fa fa-paw"></i>感谢您的阅读-------------</div>
    {% endif %}
</div>

```

### 主题next加访问量

```
//打开\themes\next\layout\_partials\footer.swig文件,
//在copyright前加上
<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
```

```
//然后再合适的位置添加显示统计的代码
<div class="powered-by">
<i class="fa fa-user-md"></i><span id="busuanzi_container_site_uv">
  本站访客数:<span id="busuanzi_value_site_uv"></span>
</span>
</div>
//在这之前加<div class="powered-by">..'footer.powered'
```

```
//pv的方式，单个用户连续点击n篇文章，记录n次访问量
<span id="busuanzi_container_site_pv">
    本站总访问量<span id="busuanzi_value_site_pv"></span>次
</span>
//uv的方式，单个用户连续点击n篇文章，只记录1次访客数
<span id="busuanzi_container_site_uv">
  本站总访问量<span id="busuanzi_value_site_uv"></span>次
</span>
```



### 主题next网站字数统计

```
//根目录下，然后运行如下代码
npm install hexo-wordcount --save
//在/themes/next/layout/_partials/footer.swig文件尾部加上
<div class="theme-info">
  <div class="powered-by"></div>
  <span class="post-count">博客全站共{{ totalcount(site) }}字</span>
</div>
```



### 主题next设置网站图标

```
//找一张（32*32）的ico图标
//名称改为favicon.ico
//图标放在/themes/next/source/images里
//修改主题配置文件
# Put your favicon.ico into `hexo-site/source/` directory.
favicon: /favicon.ico
```

### 主题next统计功能

```
//在根目录下安装 hexo-wordcount
npm install hexo-wordcount --save
//主题的配置文件中
# Post wordcount display settings
# Dependencies: https://github.com/willin/hexo-wordcount
post_wordcount:
  item_text: true
  wordcount: true
  min2read: true
```

### 主题next统计置顶功能

```
//修改 hero-generator-index 插件的文件
//node_modules/hexo-generator-index/lib/generator.js 代码替换
'use strict';
var pagination = require('hexo-pagination');
module.exports = function(locals){
  var config = this.config;
  var posts = locals.posts;
    posts.data = posts.data.sort(function(a, b) {
        if(a.top && b.top) { // 两篇文章top都有定义
            if(a.top == b.top) return b.date - a.date; // 若top值一样则按照文章日期降序排
            else return b.top - a.top; // 否则按照top值降序排
        }
        else if(a.top && !b.top) { // 以下是只有一篇文章top有定义，那么将有top的排在前面（这里用异或操作居然不行233）
            return -1;
        }
        else if(!a.top && b.top) {
            return 1;
        }
        else return b.date - a.date; // 都没定义按照文章日期降序排
    });
  var paginationDir = config.pagination_dir || 'page';
  return pagination('', posts, {
    perPage: config.index_generator.per_page,
    layout: ['index', 'archive'],
    format: paginationDir + '/%d/',
    data: {
      __index: true
    }
  });
};
```

```
//文章中添加 top 值，数值越大文章越靠前
---
title: 解决Charles乱码问题
date: 2017-05-22 22:45:48
tags: 技巧
categories: 技巧
copyright: true
top: 100
---

```

### 主题next修改字体

```
//打开\themes\next\source\css\ _variables\base.styl文件，
//将$font-size-base改成16px
$font-size-base            =16px
//或者
$font-size-base            :1.2em
```

