---
title: Doris的SQL
date: 2024-02-22 15:53:34
tags:
    - SQL
categories:
    - Doris
description:
    - 记录一些会常用的SQL
---

### 查看fe配置项

``` sql
ADMIN SHOW FRONTEND CONFIG [LIKE "pattern"];
```

### 设置fe配置项

``` sql
ADMIN SET FRONTEND CONFIG ("key" = "value");
```

### 查看系统变量

``` sql
SHOW VARIABLES [LIKE "pattern"];
```

### 刷新指定 Catalog/Database/Table 的元数据

``` sql
REFRESH CATALOG catalog_name;
REFRESH DATABASE [catalog_name.]database_name;
REFRESH TABLE [catalog_name.][database_name.]table_name;
```

### 创建CirroData Catalog

``` sql
CREATE
    CATALOG `cirrodata_dailybuild11` PROPERTIES(
        "type" = "cirrodata" ,
        "password" = "*XXX" ,
        "hadoop.username" = "hdfs" ,
        "fs.defaultFS" = "hdfs://nameservice1" ,
        "dfs.nameservices" = "nameservice1" ,
        "dfs.namenode.rpc-address.nameservice1.namenode93" = "172.16.44.57:8020" ,
        "dfs.namenode.rpc-address.nameservice1.namenode107" = "172.16.44.58:8020" ,
        "dfs.ha.namenodes.nameservice1" = "namenode93,namenode107" ,
        "dfs.client.failover.proxy.provider.nameservice1" = "org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider" ,
        "create_time" = "2024-02-19 18:51:57.762" ,
        "cirro.version" = "2.15" ,
        "cirro.root_name" = "cirrodata_dailybuild11" ,
        "cirro.database" = "REG_PARTITION" ,
        "cirro.cluster_name" = "cirrodata_dailybuild11"
    )
;
```

### 查看指定表的列信息

``` sql
SHOW [FULL] COLUMNS FROM tbl;
```

### [查看查询计划](https://doris.apache.org/zh-CN/docs/advanced/best-practice/query-analysis/#%E6%9F%A5%E7%9C%8B%E6%9F%A5%E8%AF%A2%E8%AE%A1%E5%88%92)

``` sql
EXPLAIN VERBOSE select ...
```

### [查看查询 Profile](https://doris.apache.org/zh-CN/docs/advanced/best-practice/query-analysis/#%E6%9F%A5%E7%9C%8B%E6%9F%A5%E8%AF%A2-profile)

``` sql
--
SET is_report_success=true;

-- 执行查询

-- 获取profile列表，复制query_id，如：c257c52f93e149ee-ace8ac14e8c9fef9
show query profile "/";

-- 查看整体执行计划树，每个节点都标注了自己所属的 Fragment
show query profile "/c257c52f93e149ee-ace8ac14e8c9fef9";

-- 查看具体 Fragment 下的 Instance 列表
show query profile "/c257c52f93e149ee-ace8ac14e8c9fef9/1";

-- 查看具体 Instance
show query profile "/c257c52f93e149ee-ace8ac14e8c9fef9/1/c257c52f93e149ee-ace8ac14e8c9ff03";
```

### 查看版本

``` sql
SHOW VARIABLES LIKE "version_comment";
```