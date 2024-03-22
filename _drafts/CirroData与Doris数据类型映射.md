---
title: CirroData与Doris数据类型映射
date: 2024-02-06 11:18:52
tags:
    - 数据类型
categories:
    - Doris
description:
    - CirroData 与 Doris 的数据类型映射关系
---

## 文件分析函数场

| CirroData Type | Doris Type | Comment |
| --- | --- | --- |
| int | int | |
| long | bigint | |
| double | double | |
| char(n), [ if(n >= 1 && n <=255) ] | char(n) | |
| char(n), [ if(n > 255) ] | varchar(n) | 去除末尾空白字符 |
| varchar | varchar | |
| date | datetime(0) | |
| timestamp(n), [ if(n >=0 && n <=6) ] | datetime(6) |
| timestamp(n), [ if(n > 6 && n <=9) ] | datetime(6) | 小数为截断处理 |
| YMInterval | varchar(13) | |
| DSInterval | varchar(29) | |
| number | varchar(130) | 所有number类型 |
| lob | | 暂不支持 |

## Multi-Catalog场景

| CirroData Type | Doris Type | Comment |
| --- | --- | --- |
| int | int | |
| long | bigint | |
| double | double | |
| char(n)<br> [ if(n >= 1 && n <=255) ] | char(n) | |
| char(n)<br> [ if(n > 255) ] | varchar(n) | 去除末尾空白字符 |
| varchar | varchar | |
| date | datetime(0) | |
| timestamp(n)<br>[ if(n >=0 && n <=6) ] | datetime(n) |
| timestamp(n)<br>[ if(n > 6 && n <=9) ] | datetime(6) | 小数为截断处理 |
| YMInterval | varchar(13) | |
| DSInterval | varchar(29) | |
| number(p) / number(p,0) | TINYINT/SMALLINT/INT/BIGINT/LARGEINT | Doris会根据p的大小来选择对应的类型：<br> p < 3 -> TINYINT;<br> p < 5 -> SMALLINT;<br> p < 10 -> INT;<br> p < 19 -> BIGINT;<br> p >= 19 -> LARGEINT |
| number(p,s)<br>[ if(s>0 && s <= 38 && p >= s) ] | DECIMAL(p,s) | |
| number(p,s)<br>[ if(s>0 && s <= 38 && p < s) ] | DECIMAL(s,s) | |
| number(p,s)<br>[ if(s<0) && p + \|s\| <= 38 ] | TINYINT/SMALLINT/INT/BIGINT/LARGEINT | s<0的情况下, Doris会将p设置为 p+\|s\| , 并进行和number(p) / number(p,0)一样的映射 |
| number(p,s)<br><div style="white-space: nowrap">[ if (s < 0 && p + \|s\| > 38) \|\| (s > 38) ]</div> | varchar(130) | |
| number | varchar(130) | 未指定p和s |
| lob | | 暂不支持 |