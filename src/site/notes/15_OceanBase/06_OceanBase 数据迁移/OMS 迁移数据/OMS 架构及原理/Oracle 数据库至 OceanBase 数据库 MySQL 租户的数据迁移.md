---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/OMS 迁移数据/OMS 架构及原理/Oracle 数据库至 OceanBase 数据库 MySQL 租户的数据迁移/","dgPassFrontmatter":true}
---


### Oracle 数据库至 OceanBase 数据库 MySQL 租户的数据迁移
迁移 【`Oracle 数据库`】的数据至 【`OceanBase 数据库 MySQL 租户`】时，OMS 会对【`无唯一键表`】添加【`列`】和【`唯一索引`】；

Oracle 数据库至 OceanBase 数据库 MySQL 租户的数据迁移，OMS 会自动添加隐藏列和隐藏列注释至 `CREATE TABLE` 语句中；
```sql
CREATE TABLE "table5" (
    "V1" CHAR(20) BINARY DEFAULT 'test',
    `OMS_OBJECT_NUMBER` BIGINT COMMENT 'Reserved for data migration tasks of OMS',
    `OMS_RELATIVE_FNO` BIGINT COMMENT 'Reserved for data migration tasks of OMS',
    `OMS_BLOCK_NUMBER` BIGINT COMMENT 'Reserved for data migration tasks of OMS',
    `OMS_ROW_NUMBER` BIGINT COMMENT 'Reserved for data migration tasks of OMS',
UNIQUE KEY `UK_{table}_OMS_ROWID` (
    `OMS_OBJECT_NUMBER`, 
    `OMS_RELATIVE_FNO`, 
    `OMS_BLOCK_NUMBER`, 
    `OMS_ROW_NUMBER`
    )
);
```


#### 1 
您可以执行下述命令（其中 {schema}、{table} 需要替换为实际的库名和表名），判断是否为无唯一键表。如果无返回结果，即可视为无唯一键表；

```sql
select 1 
from ALL_CONS_COLUMNS DCC 
join ALL_CONSTRAINTS DC ON DCC.CONSTRAINT_NAME = DC.CONSTRAINT_NAME AND DCC.OWNER = DC.OWNER 
join ALL_TAB_COLUMNS DTC on DCC.COLUMN_NAME = DTC.COLUMN_NAME AND DCC.OWNER = DTC.OWNER AND DCC.TABLE_NAME = DTC.TABLE_NAME 
where DCC.owner = {schema} AND DCC.TABLE_NAME = {table} and DC.constraint_type in ('U', 'P') 
group by DC.OWNER, DC.TABLE_NAME, DC.CONSTRAINT_NAME 
having count(*) = count(CASE DTC.NULLABLE WHEN 'Y' THEN NULL ELSE 1 END);
```

- 添加，删除列
```sql
-- 添加列
	ALTER TABLE `{schema}`.`{table}` ADD COLUMN `OMS_OBJECT_NUMBER` BIGINT;
	ALTER TABLE `{schema}`.`{table}` ADD COLUMN `OMS_RELATIVE_FNO` BIGINT;
	ALTER TABLE `{schema}`.`{table}` ADD COLUMN `OMS_BLOCK_NUMBER` BIGINT;
	ALTER TABLE `{schema}`.`{table}` ADD COLUMN `OMS_ROW_NUMBER` BIGINT;

-- 删除列
ALTER TABLE `{schema}`.`{table}`  DROP COLUMN `OMS_OBJECT_NUMBER`, DROP COLUMN `OMS_RELATIVE_FNO`, DROP COLUMN `OMS_BLOCK_NUMBER`, DROP COLUMN `OMS_ROW_NUMBER`;
```

- 添加唯一索引
```sql
-- 添加唯一索引
	-- 有分区字段
	CREATE UNIQUE KEY `UK_{table}_OMS_ROWID` (`OMS_OBJECT_NUMBER`, `OMS_RELATIVE_FNO`, `OMS_BLOCK_NUMBER`, `OMS_ROW_NUMBER`, `{partition_col_0}`, `{partition_col_1}`) LOCAL;
	-- 没有分区字段
	CREATE UNIQUE KEY `UK_{table}_OMS_ROWID` (`OMS_OBJECT_NUMBER`, `OMS_RELATIVE_FNO`, `OMS_BLOCK_NUMBER`, `OMS_ROW_NUMBER`);

-- 删除唯一索引
DROP INDEX UK_{table}_OMS_ROWID ON {table};
```


### 参考文档

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">



### 参考文档
1. *数据迁移服务隐藏列机制说明*：[4.2.3](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000988071)，；




</div></div>



