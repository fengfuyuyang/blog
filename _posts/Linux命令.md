---
title: Linux 命令
date: 2023-11-30 17:04:19
tags:
categories:
description: 记录有用但不常用的命令
---

## 查看当前编译环境中所有宏定义

``` bash
gcc -E -dM - < /dev/null
```