---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/创建组合触发器_Oracle 租户/","dgPassFrontmatter":true}
---


### 创建组合触发器_Oracle 租户
一个组合触发器中可以包含行前、行后、语句前、语句后四种触发事件中的一到四种或只包含一个 INSTEAD OF 触发事件；

#### 2 概述

在表或可编辑视图上创建的组合触发器可以在多个时间点触发。组合触发器按照每个时间点分隔成段，每个时间段都有自己的可执行部分和异常处理部分（可选的），所有这些部分都可以访问一个通用的 PL 状态。通用状态在触发语句开始时建立，在触发语句完成时销毁，即使触发语句导致错误时也不会受到影响。

#### 3 组合触发器的使用限制

> [!NOTE] 组合 DML 触发器具有以下限制：
> 1.  `OLD`、`NEW` 和 `PARENT` 不能出现在声明部分，也不能出现在`BEFORE STATEMENT` 部分或 `AFTER STATEMENT` 部分；
> 2. 只有 `BEFORE EACH ROW` 部分可以更改 `NEW` 的值；
> 3. 一个时间段不能处理在另一个时间段中引发的异常；
> 4. 如果一个时间段内包含 `GOTO` 语句，则 `GOTO` 语句的目标必须在同一时间段中；

#### 4 创建触发器


### 参考文档
1. [创建组合触发器_V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000641873)，；



