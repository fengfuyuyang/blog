---
title: ceph安装部署
date: 2022-10-15 10:32:34
tags:
categories:
  - ssh
description:
  - 安装部署ceph
---

### 所有节点创建无密码的root访问权限的用户
``` bash
ssh root@HOST_NAME
adduser USER_NAME
paswd USER_NAME
```

切换到root用户执行
``` bash
cat << EOF >/etc/sudoers.d/USER_NAME
USER_NAME ALL = (root) NOPASSWD:ALL
EOF
```

例如
``` bash
cat << EOF >/etc/sudoers.d/cephadm
cephadm ALL = (root) NOPASSWD:ALL
EOF
```

调整文件权限
``` bash
chmod 0440 /etc/sudoers.d/USER_NAME
```


### 配置免密

所有节点
``` bash
ssh-keygen
```

管理节点
``` bash
ssh-copy-id -i ~/.ssh/id_rsa.pub USER_NAME@HOST_NAME
```

创建config文件，
``` bash
vim ~/.ssh/config
```

```
Host host01
   Hostname HOST_NAME
   User USER_NAME
Host host02
   Hostname HOST_NAME
   User USER_NAME
...
```

修改文件权限
``` bash
chmod 600 ~/.ssh/config
```

### 配置主机名解析
修改hosts文件
```
127.0.0.1	localhost localhost.localdomain localhost4 localhost4.localdomain4
::1	        localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.159.129 admin
192.168.159.132 node1
192.168.159.133 node2
192.168.159.134 node3
```

### 修改hostname方便识别

``` bash
hostnamectl set-hostname HOST_NAME
```


### 安装部署工具

``` bash
sudo apt install -y cephadm
```

或
```
curl --silent --remote-name --location https://github.com/ceph/ceph/raw/quincy/src/cephadm/cephadm
chmod +x cephadm

--会出错，建议使用阿里或清华源
./cephadm add-repo --version 17.2.4
```

### 添加清华源

``` bash
wget -q -O- 'https://download.ceph.com/keys/release.asc' | sudo apt-key add -
sudo apt-add-repository 'deb https://mirrors.tuna.tsinghua.edu.cn/ceph/debian-octopus/ buster main'
sudo apt update
```


### 添加存储库的命令软件

```
sudo apt install software-properties-common
```
