---
title: SHOW EXPORT
date: 2024-02-21 15:48:33
tags:
categories:
description:
---


## SHOW DATA

### 字段（都是string类型）

* TableName：表名
* IndexName：索引名
* Size：所有副本的总数据量
* ReplicaCount：副本数
* RowCount：行数，以多个副本中，行数最大的那个副本为准
* RemoteSize：远端数据量
* Total：汇总行
* Quota：当前数据库设置的配额
* Left： 剩余配额

### 用户权限要求

有所查询数据库或表的读取权限

## SHOW EXPORT;

### 字段（都是string类型）

* JobId：作业的唯一 ID
* Label：Export任务的label
* State：作业状态：
  * PENDING：作业待调度
  * EXPORTING：数据导出中
  * FINISHED：作业成功
  * CANCELLED：作业失败
* Progress：作业进度。该进度以查询计划为单位。假设一共 10 个线程，当前已完成 3 个，则进度为 30%。
* TaskInfo：以 Json 格式展示的作业信息：
  * db：数据库名
  * tbl：表名
  * partitions：指定导出的分区。`空`列表 表示所有分区。
  * column_separator：导出文件的列分隔符。
  * line_delimiter：导出文件的行分隔符。
  * tablet num：涉及的总 Tablet 数量。
  * broker：使用的 broker 的名称。
  * coord num：查询计划的个数。
  * max_file_size：一个导出文件的最大大小。
  * delete_existing_files：是否删除导出目录下已存在的文件及目录。
  * columns：指定需要导出的列名，空值代表导出所有列。
  * format：导出的文件格式
* Path：远端存储上的导出路径。
* CreateTime/StartTime/FinishTime：作业的创建时间、开始调度时间和结束时间。
* Timeout：作业超时时间。单位是秒。该时间从 CreateTime 开始计算。
* ErrorMsg：如果作业出现错误，这里会显示错误原因。
* OutfileInfo：如果作业导出成功，这里会显示具体的`SELECT INTO OUTFILE`结果信息。

### 用户权限要求

有所查询数据库的读取权限