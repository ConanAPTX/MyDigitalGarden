---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/99_内部表介绍/gv, sysstat/","dgPassFrontmatter":true}
---


### gv$sysstat
#### 1 功能
展示所有 OBServer 的租户级别的统计事件信息；

> [!NOTE] 修改记录：
> 1. 从 V1.4 版本开始引入；

#### 2 相关视图
`__all_virtual_sysstat`：，；

#### 3 常用查询

```sql

```


#### 4 字段说明
1. `CON_ID`：bigint(20)，NO，租户 ID；
2. `SVR_IP`：varchar(46)，NO，信息所在服务器的 IP 地址；
3. `SVR_PORT`：bigint(20)，NO，信息所在服务器的端口号；
4. `STATISTICS#`：bigint(20)，NO，统计事件的下标；
5. `VALUE`：bigint(20)，NO，统计事件对应的结果数值；
6. `STAT_ID`：bigint(20)，NO，统计事件的 ID；
7. `NAME`：varchar(64)，NO，统计事件的名称；
8. `CLASS`：bigint(20)，NO，统计事件所属的类型；


### 参考文档
1. [gv$sysstat_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945507)，展示所有 OBServer 的租户级别的统计事件信息；




