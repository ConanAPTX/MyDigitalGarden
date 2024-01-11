---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/99_内部表介绍/GV$MEMORY，GV$OB_MEMORY/","dgPassFrontmatter":true}
---


### GV\$MEMORY，GV\$OB_MEMORY
#### 1 功能
该视图用于展示所有服务器上每个租户的内存使用状况；

> [!NOTE] 修改记录：
> 1. 该视图名从 V4.0.0 版本开始由 GV\$MEMORY 调整为 GV\$OB_MEMORY；

#### 2 常用查询


#### 3 相关视图
oceanbase.\_\_all_virtual_memory_info，；

#### 4 字段说明
##### 4.1 GV\$MEMORY


`CON_ID`：NUMBER(38)，NO，租户 ID；
`SVR_IP`：VARCHAR2(32)，NO，服务器的 IP 地址；
`SVR_PORT`：NUMBER(38)，NO，服务器端口号；
`CONTEXT`：VARCHAR2(256)，NO，内存模块名称；
`COUNT`：decimal(20, 0)，NO，alloc 与 free 的差值，当前该 mod 使用中的内存单元个数；
`USED`：decimal(20, 0)，NO，该 mod 当前使用的内存数值，单位：Byte；
`ALLOC_COUNT`：decimal(20, 0)，NO，内存申请的总个数；
`FREE_COUNT`：decimal(20, 0)，NO，内存释放的总个数；

##### 4.2 GV\$OB_MEMORY

该视图名从 V4.0.0 版本开始由 GV\$MEMORY 调整为 GV\$OB_MEMORY；

`CON_ID`：NUMBER(38)，NO，租户 ID；
`SVR_IP`：VARCHAR2(46)，NO，服务器的 IP 地址；
`SVR_PORT`：NUMBER(38)，NO，服务器端口号；
`CTX_NAME`：VARCHAR2(256)，NO，内存所属 CTX 名称；
`MOD_NAME`：VARCHAR2(256)，NO，内存所属 Mod 名称；
`COUNT`：NUMBER，NO，alloc 与 free 的差值，当前该 mod 使用中的内存单元个数；
`HOLD`：NUMBER，NO，该 mod 当前占有的内存大小；
`USED`：NUMBER，NO，该 mod 当前使用的内存数值，单位：Byte；


### 参考文档
1. `gv$memory`：[Oracle_3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944457)，[MySql_3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945209)，；
2. `GV$OB_MEMORY`：[sys 租户_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219770)，[Oracle_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219135)，[MySql_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219821)，；



