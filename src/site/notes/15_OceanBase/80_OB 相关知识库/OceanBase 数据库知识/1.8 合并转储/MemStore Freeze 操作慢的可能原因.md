---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.8 合并转储/MemStore Freeze 操作慢的可能原因/","dgPassFrontmatter":true}
---


### MemStore Freeze 操作慢的可能原因


#### 1 适用版本
OceanBase 数据库 V2.x 和 V3.x 版本；

#### 2 问题现象
如在 `observer.log` 中存在以下内容，说明冻结耗时过大。

```
[2022-09-25 05:21:52.454510] WARN  [LIB] ~ObTimeGuard (utility.h:774) [32094][1474][YB42C20031C9-0005E943DD9A2E24] [lt=4] [dc=0] destruct(*this=time guard 'freeze' cost too much time, used=14324451)
```


#### 3 可能原因
1. 主副本卡冻结的原因通常是：日志同步慢；
	1. 冻结 MEMTable 需要保证所有使用这个 MEMTable 的日志同步成功，冻结慢很可能是日志同步较慢；
2. 从副本卡冻结的原因通常是：日志回放慢；
	1. OceanBase 数据库的日志回放可以是乱序的，有可能存在较早的事务日志在较晚的事务日志回放之后进行同步、回放的场景。如果当前冻结的数据的最新版本是 100，则要求 100 之前的所有日志都回放完成。如果数据版本为 99 的事务日志没有回放完成，则需要等待其回放完成后才能冻结。


#### 4 解决方式





### 参考文档
1. [MemStore Freeze 操作慢的可能原因](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000209903?back=kb)，；

