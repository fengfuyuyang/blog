---
title: OLAP-3.5-INSERT-UPDATE-问题定位
tags:
  - INSERT
  - UPDATE
  - GDB
categories:
  - OLAP3.5
description:
  - 通过gdb调试，快速定位 insert 与 update 相关问题
date: 2024-01-26 15:21:07
---


# 案例1

## 问题描述

以问题单 #48164 为例:

定义decimal(m)，加载超过精度数值，执行未报错 与mysql规则不同
``` sql
-- 定义decimal(m)，加载超过精度数值，执行未报错,  定义decimal(m,d)，加载超过精度数值，执行报错 ； myslq以上两种都执行报错

-- 1 .decimal(m)：

drop table ceshi_1;
create table ceshi_1 (a int,b DECIMAL(10)) DISTRIBUTED BY HASH(a) BUCKETS 1;
insert into ceshi_1  values(1,1234567890123);  -- 3.5正常加载，mysql执行报错
select * from ceshi_1;

-- 2.decimal(m,d)：

drop table ceshi_2;
create table ceshi_2 (a int,b DECIMAL(10,1)) DISTRIBUTED BY HASH(a) BUCKETS 1;
insert into ceshi_2  values(1,9999999990.1);  --3.5执行报错，mysql执行报错
select * from ceshi_2;
```

注意：虽然单子中没有文字说明，但加载操作实际是在严格模式下进行的


## 问题定位

1. 执行下相同sql, 看下执行结果

![Alt text](/images/OLAP-3-5-INSERT-UPDATE-问题定位/查询结果.png)

由执行结果可以看出，1234567890123 被截断为 1234567890 了，所以才加载成功了


1. 由于fe和be都有可能处理数据，所以先确定数据是否是在be侧进行处理的

- 编译debug版be，通过 gdb attach <doris_be_pid> 进行调试be

在 be/src/vec/exprs/vliteral.cpp 中 VLiteral::execute 函数中打上断点

``` gdb
b be/src/vec/exprs/vliteral.cpp:63
```

所有 column 的构建都会通过 VLiteral, 在不清楚数据处理流程的情况下，都可以打这个断点，
并且这里也可以 bt 打印堆栈，方便理清代码处理流程

- 然后打印VLiteral的成员函数 value(), 就可以看到从fe侧传递过来的数值是什么

![Alt text](/images/OLAP-3-5-INSERT-UPDATE-问题定位/整数decimal.png)

由上图，gdb 调试结果可以看到，value() 的结果是 "1234567890"， 这说明数据是在 fe 侧被截断的

为什么 9999999990.1 加载就会正常报错呢？同样可以查看下

![Alt text](/images/OLAP-3-5-INSERT-UPDATE-问题定位/小数decimal.png)

value() 的结果为 "9999999990.1" , _expr_name 的结果为 "Decimal(11, 1)",
说明 fe 侧对小数没有直接处理和判断是否符合精刻度要求，而是原值传递，判断是否符合要求是在 be 侧进行处理的

# 案例2

## 问题描述

``` sql
-- boolean类型，插入非0 1的整型和小数，3.5处理与mysql不同
-- 注意：即使3.5版本不做改动，boolean插入小数为0 这个需要确认是否正确

drop table lgp_lgp_2;
create table lgp_lgp_2(id int,bool_1 boolean)
DISTRIBUTED BY HASH(id) BUCKETS 10;

insert into lgp_lgp_2 select 1,12;
insert into lgp_lgp_2 select 2,4.3;

select * from lgp_lgp_2

-- 3.5版本结果：
-- 1  1
-- 2  0
--
-- mysql结果：
-- 1 12
-- 2 4
```

## 问题定位

1. 和案例1一样，gdb 调试，看从fe传递下来的数据是什么样的

![Alt text](/images/OLAP-3-5-INSERT-UPDATE-问题定位/boolean.png)

由上图可以看出，数据没有被修改，但数据的类型是int8，而不是boolean

2. 查看堆栈

![Alt text](/images/OLAP-3-5-INSERT-UPDATE-问题定位/堆栈1.png)

通过堆栈可以看出，是 VCastExpr::open 调用的，说明存在 cast 转换，
切换到该栈帧， 打印 _expr_name 为 "(CAST Int8(Int8) TO UInt8)"，则验证了猜想，insert的结果是通过cast转换而来

同样，查看 `insert into lgp_lgp_2 select 2,4.3;` 执行结果怎么来的

![Alt text](/images/OLAP-3-5-INSERT-UPDATE-问题定位/小数转boolean.png)

![Alt text](/images/OLAP-3-5-INSERT-UPDATE-问题定位/堆栈2.png)

可以清楚的看到 4.3 是通过 "(CAST Decimal(2, 1)(Decimal(2, 1)) TO UInt8)" 转换为0的
