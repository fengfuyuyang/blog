---
title: olap问题定位
date: 2024-03-11 10:26:35
tags:
categories:
description:
    - 总结olap问题定位方法
---

### 查找导致崩溃的sql

1. gdb调试core文件，找到包含 RuntimeState* state 变量的栈帧
2. 执行命令`printf "query_id: %lx-%lx\n", state._query_id.hi, state._query_id.lo` 获取query_id

例如：

```
(gdb) f 25
#25 0x00005556b1dd3f4c in doris::vectorized::AggregationNode::open (this=0x7f774952a200, state=0x7f773ede8a00)
    at /root/doris/be/src/vec/exec/vaggregation_node.cpp:541
541	/root/doris/be/src/vec/exec/vaggregation_node.cpp: 没有那个文件或目录.
(gdb) printf "query_id: %lx-%lx\n", state._query_id.hi, state._query_id.lo
query_id: 4aa8d4efda444444-b1e29f49a875f45e
```

3. 到master节点（一般是leader节点）下的master日志中执行以下命令

``` bash
grep -ar "query_id" master.audit.log* | perl -ne 'if(/Stmt=OriginStatement{originStmt='\''(.*)'\'', idx=.*/){print"$1\n"}' | uniq
```

例如：

``` bash
grep -ar "4aa8d4efda444444-b1e29f49a875f45e" master.audit.log* | perl -ne 'if(/Stmt=OriginStatement{originStmt='\''(.*)'\'', idx=.*/){print"$1\n"}' | uniq
```

过滤出如下sql

``` sql
SELECT CONCAT(81732, '-', -1, '-', 'CLOB_1_2G') AS `id`,          0 AS `catalog_id`,          11002 AS `db_id`,          81732 AS`tbl_id`,          -1 AS `idx_id`,          'CLOB_1_2G' AS `col_id`,          NULL AS `part_id`,          COUNT(1) AS `row_count`,          NDV(`CLOB_1_2G`) AS `ndv`,          COUNT(1) - COUNT(`CLOB_1_2G`) AS `null_count`,          SUBSTRING(CAST(MIN(`CLOB_1_2G`) AS STRING), 1, 1024) AS `min`,          SUBSTRING(CAST(MAX(`CLOB_1_2G`) AS STRING), 1, 1024) AS `max`,          SUM(LENGTH(`CLOB_1_2G`)) AS `data_size`,          NOW() AS `update_time`  FROM `internal`.`default_cluster:regression`.`base_dim_prov_dti_number_clob`
```
