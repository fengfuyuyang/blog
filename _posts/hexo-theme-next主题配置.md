---
title: hexo-theme-next主题配置
date: 2022-07-02 23:26:41
tags:
  - next
  - 主题
  - hexo
categories:
  - hexo
---

hexo博客使用[next主题](https://theme-next.js.org/)

<!-- more -->

## 安装
### 下载主题
``` bash
git clone https://github.com/next-theme/hexo-theme-next themes/next
```

### 启用主题
修改博客配置文件[^1],启用next主题
[^1]: 博客根目录下:_config.yml
``` yml
theme: next
```

## next主题配置

### 更换主题风格
next提供4种主题风格，可根据个人喜好选择，修改主题配置文件[^2]
[^2]: next主题目录下:themes/next/_config.yml
``` yml
# Schemes
scheme: Muse
#scheme: Mist
#scheme: Pisces
#scheme: Gemini
```

### 开启代码块复制按钮
修改主题配置文件
有default，flat，mac三种代码块复制风格供选择
``` yml
  # Add copy button on codeblock
  copy_button:
    enable: true
    # Available values: default | flat | mac
    style: mac
```

### 开启浏览进度
修改主题配置文件
1. 返回顶部按钮显示百分比
``` yml
  # Scroll percent label in b2t button.
  scrollpercent: true
```
2. 顶部显示进度条
``` yml
reading_progress:
  enable: true
```

### 开启书签
会保留浏览进度
修改主题配置文件
``` yml
bookmark:
  enable: true
```

### 开启右上角github banner
修改主题配置文件
可添加标语`title`
``` yml
github_banner:
  enable: true
  permalink: https://github.com/yourname
  title: Follow me on GitHub
```

### 显示浏览进度
修改主题配置文件
1. 返回按钮
``` yml
  # Scroll percent label in b2t button.
  scrollpercent: true
```

2. 顶部进度条
``` yml
reading_progress:
  enable: true
```


### 去掉底部`Powered by Hexo & NexT`
修改主题配置文件
``` yml
# Powered by Hexo & NexT
powered: false
```

### 启用菜单
修改主题配置文件
``` yml
menu:
  home: / || fa fa-home
  #about: /about/ || fa fa-user
  tags: /tags/ || fa fa-tags
  categories: /categories/ || fa fa-th
  archives: /archives/ || fa fa-archive
  #schedule: /schedule/ || fa fa-calendar
  #sitemap: /sitemap.xml || fa fa-sitemap
  #commonweal: /404/ || fa fa-heartbeat
```
{% tabs menu, 1 %}
<!-- tab 启用tag -->
博客根目录执行以下命令
``` bash
hexo new page "tags"
```
修改`source/tags/index.md`文件
添加`type`和`layout`
```
---
title: tags
date: 2023-05-10 11:32:36
type: tags
layout: "tags"
---
```
<!-- endtab -->

<!-- tab 启用categories -->
博客根目录执行以下命令
``` bash
hexo new page "categories"
```
修改`source/categories/index.md`文件
添加`type`和`layout`
```
---
title: categories
date: 2023-05-10 11:32:36
type: categories
layout: "categories"
---
```
<!-- endtab -->
{% endtabs %}

#### 添加社交按钮
修改主题配置文件
``` yml
social:
  GitHub: https://github.com/yourname || fab fa-github
  #E-Mail: mailto:yourname@gmail.com || fa fa-envelope
  Weibo: https://weibo.com/yourname || fab fa-weibo
  #Twitter: https://twitter.com/yourname || fab fa-twitter
  #FB Page: https://www.facebook.com/yourname || fab fa-facebook
  #StackOverflow: https://stackoverflow.com/yourname || fab fa-stack-overflow
  #YouTube: https://youtube.com/yourname || fab fa-youtube
  #Instagram: https://instagram.com/yourname || fab fa-instagram
  #Skype: skype:yourname?call|chat || fab fa-skype
```
可以添加其他社交链接,末尾图标可以从https://fontawesome.com/icons搜索添加
例如QQ和微信
``` yml
  QQ: tencent://message/?uin=qq号 || fab fa-qq
  WeChat: 微信号 || fab fa-weixin
```
链接也可使用图片，例如微信使用二维码图片
1. 先将图片`weixin.jpg`上传至`themes/next/source/images`目录下
2. 修改使用图片路径
``` yml
  WeChat: /images/weixin.jpg || fab fa-weixin
```

### 开启版权功能
修改主题配置文件
``` yml
creative_commons:
  # Available values: by | by-nc | by-nc-nd | by-nc-sa | by-nd | by-sa | cc-zero
  license: by-nc-sa
  # Available values: big | small
  size: small
  sidebar: true
  post: true
```

## next主题美化

### 设置头像
1. 上传头像到`themes/next/source/images`目录下
2. 修改主题配置文件文件,替换url后内容为头像路径
``` yml
# Sidebar Avatar
avatar:
  # Replace the default image and set the url here.
  url: /images/head_portrait.jpg
  # If true, the avatar will be displayed in circle.
  rounded: true
  # If true, the avatar will be rotated with the cursor.
  rotated: true
```

### 启用本地搜索
1. 安装[hexo-generator-searchdb](https://github.com/next-theme/hexo-generator-searchdb)插件
``` bash
npm install hexo-generator-searchdb
```

2. 博客配置文件，添加以下内容
``` yml
search:
  path: search.xml
  field: post
  content: true
  format: html
```

3. 修改主题配置文件
``` yml
local_search:
  enable: true
```

### 启用文章字数统计及阅读时常功能
安装[hexo-word-counter](https://github.com/next-theme/hexo-word-counter)插件
``` bash
npm install hexo-word-counter
```

### 主题界面设置
#### 添加背景图
1. 创建配置目录,博客根目录下执行
``` bash
mkdir -p source/_data
```

2. 上传背景图片，如`background.jpg`到`themes/next/source/images`目录下

3. _data目录下创建文件`styles.styl`，添加以下内容
``` css
//全局布局美化代码

body {
    background-image:url(/images/background.jpg);   /* 设置页面背景图片, url后为图片路径，若路径包含中文需加上双引号 */
    background-repeat: no-repeat;                   /* 不重复背景图片 */
    background-attachment: fixed;                   /* 固定背景图片，不随页面滚动 */
    background-position: center;                    /* 背景图片居中显示 */
}

.post-block{
        background-color: rgba(255, 255, 255, 1);    /* 文章块背景色为白色 */
        // margin-top: 24px;                         /* 顶部留出空白 */
        margin-bottom: 24px;                         /* 底部留出空白 */
        // padding: 20px;                            /* 文章块内部留白 */
        border-radius: 30px 30px 30px 30px;          /* 圆角半径为 30px */
        box-shadow: 8px 7px 2px 0 rgba(0,0,0,0.12),  /* 添加阴影效果，第一个参数为水平偏移，第二个参数为垂直偏移，第三个参数为模糊半径，第四个参数为阴影扩散程度 */
                    7px 4px 1px -2px rgba(0,0,0,0.06),
                    0 1px 5px 0 rgba(0,0,0,0.12);
}

.main-inner {
    background-color: rgba(255, 255, 255, 0);        /* 主体区域透明背景 */
}
```

4. 修改主题配置文件，去掉注释
``` yml
style: source/_data/styles.styl
```

#### 设置组件圆角
1. _data目录下创建文件`variables.styl`，添加以下内容
``` css
// 圆角设置
$border-radius-inner     = 30px 30px 30px 30px;
$border-radius           = 30px;
```

2. 修改主题配置文件，去掉注释
``` yml
variable: source/_data/variables.styl
```

### 添加看板娘
使用[ive2d-widget]https://github.com/stevenjoezhang/live2d-widget
#### 最简单的
1. _data目录下创建文件`head.njk`，添加以下内容
```
<script src="https://fastly.jsdelivr.net/gh/stevenjoezhang/live2d-widget@latest/autoload.js"></script>
```

2. 修改主题配置文件,去掉注释
``` yml
  head: source/_data/head.njk
```

``` bash
cd themes/next/source/
```

``` bash
git clone https://github.com/stevenjoezhang/live2d-widget.git
```