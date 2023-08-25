---
title: hexo博客搭建
date: 2022-06-30 16:29:30
tags:
  - hexo
  - 博客
categories:
  - 博客搭建
  - hexo
description:
  - 使用hexo博客框架搭建个人博客
---

## 环境准备

### 检查Nodejs是否安装

``` bash
node -v
npm -v
```

### 若未安装，安装Nodejs

安装最新长期服务版Nodejs
``` bash
# Using Ubuntu
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs

# Using Debian, as root
curl -fsSL https://deb.nodesource.com/setup_lts.x | bash -
apt-get install -y nodejs
```

### 测试npm是否正常

``` bash
npm install npm -g
```

### 若报错如下无权限报错

``` bash
npm ERR! Error: EACCES: permission denied, mkdir '/usr/lib/node_modules/hexo-cli'
npm ERR!  [Error: EACCES: permission denied, mkdir '/usr/lib/node_modules/hexo-cli'] {
npm ERR!   errno: -13,
npm ERR!   code: 'EACCES',
npm ERR!   syscall: 'mkdir',
npm ERR!   path: '/usr/lib/node_modules/hexo-cli'
npm ERR! }
```

官方解决方案:[Resolving EACCES permissions errors when installing packages globally](https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally)

个人采用官方第二种方案，自定义全局安装目录，操作如下:

``` bash
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
```

添加环境变量
``` bash
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.profile
source ~/.profile
```

### 若nmp访问较慢，可更换阿里源

更换npm安装源

``` bash
npm config set registry https://registry.npmmirror.com
```

若想还原官方源执行以下命令

``` bash
npm config set registry https://registry.npmjs.org
```

## hexo博客部署

### 安装hexo框架

安装hexo框架

``` bash
npm install -g hexo-cli
```

查看hexo版本，验证安装

``` bash
hexo -v
```

### 初始化博客

生成博客根目录blog(目录名可任意取), 并初始化

``` bash
hexo init blog
```

进去博客根目录，
``` bash
cd blog
```

{% note warning %}
### 注意
以下操作均在博客根目录下进行
{% endnote %}

### hexo 基本使用

创建新的文章，会给出文章的路径

``` bash
hexo new "我的第一篇文章"
```

生成博客所需的静态文件

``` bash
hexo generate
```

清理生成的文件和缓存

``` bash
hexo clean
```

部署网站，需要有自己的网站地址，下面会有使用github仓库作为域名部署

``` bash
hexo deploy
```

启动博客服务，默认地址[http://localhost:4000/](http://localhost:4000/),可在浏览器打开，查看博客内容

``` bash
hexo server
```

为避免文章未写完就被发布,可以生成草稿文章

``` bash
hexo new draft "草稿"
```

通过添加 `--draft` 命令来预览草稿

``` bash
hexo server --draft
```

写完后再发布

``` bash
hexo publish "草稿"
```

### 启用tags与categories

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

### 修改默认模板

草稿文章模板文件: scaffolds/draft.md 修改为

``` md
---
title: {{ title }}
tags:
categories:
description:
---
```

正式文章模板文件: scaffolds/post.md 修改为

``` md
---
title: {{ title }}
date: {{ date }}
tags:
categories:
description:
---
```

### 使用github仓库部署博客

#### 检查git是否安装

检查git版本，验证是否安装git

``` bash
git --version
```

若未安装，先安装git

``` bash
sudo apt install git
```

#### 配置免密登录

具体操作请参考[免密访问](https://fengfuyuyang.github.io/2022/06/29/%E5%85%8D%E5%AF%86%E8%AE%BF%E9%97%AE/)

#### 创建仓库

1. 依次点击: ☞ `右上角＋` ☞ `New reposiitory`
2. Repository name 填写必须为: 自己github的用户名.github.io
   例如: fengfuyuyang.github.io
3. 点击`Create repository`, 复制仓库地址
   例如: git@github.com:fengfuyuyang/fengfuyuyang.github.io.git

#### 配置_config.yml

修改前

``` yml
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: ''
```

添加github仓库地址，修改后内容如下

``` yml
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: git
  repo: git@github.com:fengfuyuyang/fengfuyuyang.github.io.git
  branch: master
```

#### 安装git插件和hexo使用

安装插件, 注意插件只对当前博客目录生效，若另外又建了新的blog，需要重新安装插件

``` bash
npm install --save hexo-deployer-git
```

部署到git

``` bash
hexo clean && hexo g && hexo d
```

打开浏览器，输入网址[https://fengfuyuyang.github.io](https://fengfuyuyang.github.io)，即之前创建的仓库名，就可以访问自己的博客了

hexo有很多主题供选择,博主使用的是[next主题](https://theme-next.js.org/),可参考[hexo-theme-next主题配置](https://fengfuyuyang.github.io/2022/07/02/hexo-theme-next%E4%B8%BB%E9%A2%98%E9%85%8D%E7%BD%AE/#more)进行设置

### 更换更好的markdown渲染器

1. 卸载hexo 默认渲染器

``` bash
npm un hexo-renderer-marked --save
```

2. 安装[hexo-renderer-markdown-it](https://github.com/hexojs/hexo-renderer-markdown-it)

``` bash
npm i hexo-renderer-markdown-it --save
```

3. 博客配置文件添加以下内容

``` yml
markdown:
  preset: 'default'
  render:
    html: true
    xhtmlOut: false
    langPrefix: 'language-'
    breaks: true
    linkify: true
    typographer: true
    quotes: '“”‘’'
  enable_rules:
  disable_rules:
  plugins:
    - markdown-it-abbr
    # - markdown-it-attrs
    - markdown-it-cjk-breaks
    # - markdown-it-container
    # - markdown-it-deflist
    - markdown-it-emoji
    - markdown-it-footnote
    - markdown-it-ins
    - markdown-it-mark
    - markdown-it-sub
    - markdown-it-sup
  anchors:
    level: 2
    collisionSuffix: ''
    permalink: false
    permalinkClass: 'header-anchor'
    permalinkSide: 'left'
    permalinkSymbol: '¶'
    case: 0
    separator: '-'
  images:
    lazyload: false
    prepend_root: false
    post_asset: false
```

hexo-renderer-markdown-it自带一些插件,可根据自身需求选择启用哪些插件
以下是每个插件的作用和使用示例代码：

| 插件名称 | 插件作用 | 使用语法 | 效果 |
| --- | --- | --- | --- |
| markdown-it-abbr | 用于解析缩写 | `*[HTML]: 超文本标记语言.` | HTML |
| markdown-it-attrs | 用于添加标签属性   | `{.class #id data-x=hello}`|
| markdown-it-cjk-breaks | 中英文之间自动插入空格  | 无需语法，直接安装插件即可 |
| markdown-it-container | 定义自定义块级容器 | ```::: note<br />this is a note<br />:::```|
| markdown-it-deflist | 定义自定义列表     | `Term 1`<br>`: Definition 1`<br>`Term 2`<br>`: Definition 2a`<br>`: Definition 2b` |
| markdown-it-emoji | 将emoji代码转化为真实的emoji表情符号 | `:smile:` | :smile: |
| markdown-it-footnote | 定义脚注     | `这是一个带有脚注的句子[^1]。`<br> `[^1]: 脚注内容` | 这是一个带有脚注的句子[^1]。|
| markdown-it-ins | 定义下划线 | `++加入的文本++` | ++加入的文本++ |
| markdown-it-mark | 定义高亮 | `==突出显示的文本==` | ==突出显示的文本== |
| markdown-it-sub| 定义下标 | `H~2~O` | H~2~O |
| markdown-it-sup| 定义上标 | `2^10^` | 2^10^ |

*[HTML]: 超文本标记语言.
[^1]: 脚注内容

### 使用4everland部署

国内 github pages 访问很慢，可使用 4everland 部署 hexo 加速访问

1. 浏览器访问[4everland](https://dashboard.4everland.org?invite=91API3WK),无需注册,点击`Continue with GitHub`使用github登录
2. 点击左上角`Hosting` -> `Projects` -> `New Project`
3. 赋权访问github仓库,
4. 选择hexo的存储库,点击该存储库旁的`Import`
5. 选择`Hosting Platform`为IPFS
6. 修改`Basic Configuration`下`Project Name`(可任意起名,会是部署后的网址中的一部分)
7. 修改`Build Configuration`下`Framework Preset`为Other
8. 点击底部`Deploy`按钮,等待部署完成
9. 访问博客,地址为https://<设置的ProjectName>.4everland.app
