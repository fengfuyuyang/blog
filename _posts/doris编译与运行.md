---
title: doris编译与运行
date: 2023-12-21 14:52:48
tags:
categories:
    - Doris
description: 如何通过源码编译 Doris 和运行 Doris
---

## 编译

1. 下载 Docker 镜像

``` bash
docker pull apache/doris:build-env-ldb-toolchain-latest
```

可以从官方文档[Doris 镜像](https://doris.apache.org/zh-CN/docs/install/source-install/compilation-general#%E4%BD%BF%E7%94%A8%E7%8E%B0%E6%88%90%E7%9A%84%E9%95%9C%E5%83%8F)查找需要的版本

或从[Docker Hub](https://hub.docker.com/r/apache/doris/tags) 查找

1. 下载源码

``` bash
git clone https://github.com/apache/doris.git
```

3. 运行镜像

``` bash
docker run -it \
    --network=host \
    --name mydocker \
    -v /your/local/.m2:/root/.m2 \
    -v /your/local/doris-DORIS-x.x.x-release/:/your/local/doris-DORIS-x.x.x-release/ \
    apache/doris:build-env-ldb-toolchain-latest
```

默认 Doris docker 镜像只有 root 用户，这会导致在 docker 中生成在挂载目录下的文件，也只有 root 用户有权限，
为了方便宿主机的普通用户也可以访问，可以使用以下命令更改文件所属

``` bash
sudo chown -R username:username /your/local/doris-DORIS-x.x.x-release
```

或在Doris中创建普通用户，也可以创建自己开发镜像,如下操作

- 在用户根目录下创建文件 Dockerfile

``` Dockerfile
# 以 Doris 2.0 开发环境镜像为基础镜像，根据需求自行修改基础镜像
FROM apache/doris:build-env-for-2.0
# 自定义
LABEL Name=yourname-doris Version=2.0

ARG HOST_USER
ARG HOST_UID
ARG HOST_GID

# 获取宿主机的用户名、UID 和 GID
RUN npm config set registry https://registry.npmmirror.com/ && \
    groupadd -g $HOST_GID $HOST_USER && \
    useradd -u $HOST_UID -g $HOST_GID -ms /bin/bash $HOST_USER

WORKDIR /home/$HOST_USER

# 复制.ssh文件夹和.bashrc文件
COPY ./.ssh ./.ssh
COPY ./.bashrc ./

RUN chown -R $HOST_USER:$HOST_GID ./.ssh && \
    chown $HOST_USER:$HOST_GID ./.bashrc

USER $HOST_USER
```

- 构建镜像

``` bash
docker build --pull --rm -f Dockerfile --build-arg HOST_USER=$(whoami) --build-arg HOST_UID=$(id -u) --build-arg HOST_GID=$(id -g) -t $(whoami)-doris:build-env-for-2.0 .
```

``` bash
docker run -it \
    --network=host \
    --name mydocker \
    --user $(whoami)
    -v /your/local/.m2:/home/$(whoami)/.m2 \
    -v /your/local/doris-DORIS-x.x.x-release/:/your/local/doris-DORIS-x.x.x-release/ \
    $(whoami)-doris:build-env-for-2.0
```

4. 编译

在docker容器中

``` bash
cd /your/local/doris-DORIS-x.x.x-release/
./build.sh
```

编译命令

``` bash
./build.sh                      # 同时编译 be 和 fe
./build.sh --be                 # 只编译 be
./build.sh --fe                 # 只编译 fe
./build.sh --fe --be            # 同时编译 be fe
./build.sh --fe --be --clean    # 删除并同时编译 be fe
./build.sh --fe --clean         # 删除并编译 fe
./build.sh --be --clean         # 删除并编译 be
./build.sh --be --fe --clean    # 删除并同时编译 be fe
BUILD_TYPE=Debug sh build.sh    # 编译 debug 版 be
```

## 运行

### fe配置

``` conf
priority_networks = 172.16.48.4
meta_dir = /path/to/doris-meta
```

需要手动创建 meta_dir 目录

### be配置

``` conf
priority_networks = 172.16.48.4
storage_root_path = /path/to/storage
```

需要手动创建 storage_root_path 目录

{% note danger %}
**检查文件描述符限制，be启动要求不低于65536**

``` bash
ulimit -n
```

修改方法如下：
1. 打开配置文件

``` bash
sudo vim /etc/security/limits.conf
```

2. 添加如下内容

``` conf
username hard nofile 65536
username soft nofile 65536
```
{% endnote %}

### 启动fe

``` bash
./fe/bin/start_fe.sh --deamon
```

### 启动be

``` bash
./be/bin/start_be.sh --deamon
```

### 添加backend

1. mysql 客户端连接

``` bash
mysql -uroot -h127.0.0.1 -P 9030
```

2. 执行sql

``` sql
-- ip:heartbeat_service_port (be.conf中)
ALTER SYSTEM ADD BACKEND "172.16.48.4:9050";

-- 检查 backend 是否正常
-- Alive 为 true 则表示 backend 正常
SHOW BACKENDS\G
```

## 参考资料

[通用编译](https://doris.apache.org/zh-CN/docs/install/source-install/compilation-general)