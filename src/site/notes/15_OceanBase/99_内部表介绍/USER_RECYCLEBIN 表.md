---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/99_内部表介绍/USER_RECYCLEBIN 表/","dgPassFrontmatter":true}
---


### USER_RECYCLEBIN
#### 1 功能
查看当前用户拥有的回收站的信息。其列与 `DBA_RECYCLEBIN` 中的列相同，去除了 `OWNER` 字段；


> [!NOTE] 功能适用性：
> 1. 该内容仅适用于 OceanBase 数据库企业版。OceanBase 数据库社区版仅提供 MySQL 模式；

#### 2 常用查询

```sql
-- Oracle 租户
select count(*) from sys.USER_RECYCLEBIN where TYPE in ('TABLE','INDEX');

-- MySql 租户
```


#### 3 相关视图
1. [[15_OceanBase/99_内部表介绍/DBA_RECYCLEBIN 表\|DBA_RECYCLEBIN 表]]，查看数据库中所有回收站的信息；

#### 4 字段说明
`OBJECT_NAME` ：VARCHAR2(128) ，NO ，对象的新名称；
`ORIGINAL_NAME`： 	VARCHAR2(128)，NO，对象的原始名称；
`OPERATION` ：VARCHAR2(9)，NO，暂不支持该字段，默认为 NULL；
`TYPE` ：VARCHAR2(25)，NO，对象类型；
`TS_NAME` ：VARCHAR2(30)，NO，对象所属的表的名称；
`CREATETIME` ：VARCHAR2(19)，NO，创建对象的时间；
`DROPTIME` ：VARCHAR2(19)，NO，对象进入回收站的时间；
`DROPSCN` ：NUMBER，NO，暂不支持该字段，默认为 NULL；
`PARTITION_NAME` ：VARCHAR2(128)，NO，暂不支持该字段，默认为 NULL；
`CAN_UNDROP` ：VARCHAR2(3)，NO，是否可以将对象从回收站中恢复：YES；NO；
`CAN_PURGE` ：VARCHAR2(3)，NO，是否可以将对象清除：YES；NO；
`RELATED` ：NUMBER，NO，暂不支持该字段，默认为 NULL；
`BASE_OBJECT` ：NUMBER，NO，暂不支持该字段，默认为 NULL；
`PURGE_OBJECT` ：NUMBER，NO，暂不支持该字段，默认为 NULL；
`SPACE` ：NUMBER 	NO 	暂不支持该字段，默认为 NULL；

### 参考文档
1. [USER_RECYCLEBIN_Oracle_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219267)，；


