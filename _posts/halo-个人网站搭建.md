---
title: halo-个人网站搭建
date: 2022-07-03 13:18:11
tags:
  - halo
  - 网站
  - 博客
categories:
  - 博客搭建
---

从零开始搭建halo个人网站

<!-- more -->

``` bash
sudo apt-get install default-jdk -y
java -version
```


下载halo java包

``` bash
curl -L https://github.com/halo-dev/halo/releases/download/v1.5.3/halo-1.5.3.jar --output halo.jar
```


``` bash
nohup java -jar halo.jar &
```

默认端口8090

Nginx
### nginx安装
``` bash
# 安装依赖
sudo apt-get install libpcre3 libpcre3-dev -y
sudo apt-get install openssl libssl-dev -y
# 下载nginx安装包
wget http://nginx.org/download/nginx-1.22.0.tar.gz
# 解压
tar zxvf nginx-1.22.0.tar.gz -C ./
cd nginx-1.22.0
./configure
# 编译安装
sudo make && make install
# 修改配置文件
sudo vim /usr/local/nginx/conf/nginx.conf
# 启动nginx服务
sudo /usr/local/nginx/sbin/nginx
# 修改配置后重启nginx服务
sudo /usr/local/nginx/sbin/nginx -s reload
```

server 前添加
``` conf
    upstream blog {
        server 127.0.0.1:8090;
    }
```

替换location大括号里的内容
``` conf
            proxy_pass http://blog;
            proxy_set_header HOST $host;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```


### 添加备案信息
`http://localhost:8090/admin` -> 系统 -> 博客设置 -> 基本设置 -> 页脚信息
``` conf
<a href="http://beian.miit.gov.cn/"  style="color:#f72b07" target="_blank">京 ICP 备 2022007624 号 - 1</a>
```

