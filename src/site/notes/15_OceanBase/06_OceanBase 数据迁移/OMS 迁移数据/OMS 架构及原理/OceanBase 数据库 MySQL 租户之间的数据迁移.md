---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/OMS 迁移数据/OMS 架构及原理/OceanBase 数据库 MySQL 租户之间的数据迁移/","dgPassFrontmatter":true}
---



### 文档名称

> [!NOTE] 注意事项：
> *容灾双活场景仅支持迁移有唯一键表*；


迁移 【`OceanBase 数据库 MySQL 租户`】的数据至 `OceanBase 数据库 MySQL 租户`时，OMS 会对*无唯一键表* 添加【`列`】和【`唯一索引`】；

OceanBase 数据库 MySQL 租户之间的数据迁移，OMS 会自动添加隐藏列、隐藏列注释和唯一索引至 `CREATE TABLE` 语句中；
```sql
//没有分区字段，创建隐藏列和唯一索引
create table `table2` (
    `c1` binary(1),
    `OMS_PK_INCRMT` bigint COMMENT 'Reserved for data migration tasks of OMS',
    UNIQUE KEY `UK_table2_OBPK_INCRMT` (`OMS_PK_INCRMT`)
);

//有分区字段，创建隐藏列和唯一索引
create table `partlistcolumns_key_nopri` (
    `col1` int(11),
    `col2` decimal(10,2),
    `col3` decimal(10,2),
    `col4` bit(1),
    `col5` tinyint(4),
    `col6` smallint(6),
    `OMS_PK_INCRMT` bigint COMMENT 'Reserved for data migration tasks of OMS',
    UNIQUE KEY `UK_partlistcolumns_key_nopri_OBPK_INCRMT` (`col1`,`col6`, `OMS_PK_INCRMT`) LOCAL
)
default charset=utf8mb4
default collate=utf8mb4_general_ci
PARTITION BY LIST COLUMNS (col1)
SUBPARTITION BY KEY (col6)
subpartition template (
    SUBPARTITION `p0`,
    SUBPARTITION `p1`,
    SUBPARTITION `p2`,
    SUBPARTITION `p3`
)
(
    PARTITION `p1` VALUES IN (1),
    PARTITION `p2` VALUES IN (2),
    PARTITION `p3` VALUES IN (3),
    PARTITION `p4` VALUES IN (4)
);
```




#### 1 使用示例
- 判断是否为无唯一键表。如果无返回结果，即可视为无唯一键表
```sql
-- 判断是否为无唯一键表。如果无返回结果，即可视为无唯一键表；【其中 {schema}、{table} 需要替换为实际的库名和表名；】
SELECT 1 FROM information_schema.statistics 
WHERE TABLE_SCHEMA = {schema} AND TABLE_NAME = {table} 
GROUP BY TABLE_SCHEMA, TABLE_NAME, INDEX_NAME 
HAVING count(1) = count(IF(upper(nullable) != 'YES' and NON_UNIQUE = 0, 1, NULL));
```

- 添加，删除列
```sql
-- 添加，删除列
	-- 添加列
	--语法：ALTER TABLE `{schema}`.`{table}` ADD COLUMN `OMS_PK_INCRMT` BIGINT;

	-- 删除列
	--语法：ALTER TABLE `{schema}`.`{table}` DROP COLUMN `OMS_PK_INCRMT`;
```
    
- 添加，删除唯一索引
```sql
-- 添加唯一索引
	-- 有分区字段
	--语法：CREATE UNIQUE INDEX `UK_{table}_OBPK_INCRMT` ON `{schema}`.`{table}` (`{partition_col_0}`, `{partition_col_1}`, `OMS_PK_INCRMT`) LOCAL;
	
	--没有分区字段
	--语法：CREATE UNIQUE INDEX `UK_{table}_OBPK_INCRMT` ON `{schema}`.`{table}` (`OMS_PK_INCRMT`);


-- 删除唯一索引
	--语法：DROP INDEX `UK_{table}_OBPK_INCRMT` ON `{schema}`.`{table}`;
```


### 参考文档

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">



### 参考文档
1. *数据迁移服务隐藏列机制说明*：[4.2.3](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000988071)，；




</div></div>





