---
title: Doris UPDATE 操作
date: 2023-12-27 10:10:39
tags:
categories:
description:
    - 修改或更新Doris中的数据
---

## 适用场景

- 对满足某些条件的行，修改其取值；
- 点更新，小范围更新，待更新的行最好是整个表的非常小的一部分；
- update 命令只能在 Unique 数据模型的表中执行。








## 流程图

``` Mermaid
sequenceDiagram
PInternalServiceImpl->>PInternalServiceImpl:_tablet_writer_add_block
PInternalServiceImpl->>LoadChannelMgr:add_batch
LoadChannelMgr->>LoadChannel:add_batch
LoadChannel->>TabletsChannel:add_batch
TabletsChannel->>DeltaWriter:write
DeltaWriter->>MemTable:insert
```

PInternalServiceImpl::_tablet_writer_add_block
    LoadChannelMgr::add_batch
        LoadChannel::add_batch
            TabletsChannel::add_batch
                DeltaWriter::write
                    MemTable::insert