---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/OMS 迁移数据/OMS 架构及原理/OceanBase 数据库 Oracle 租户至 Oracle 数据库的数据迁移/","dgPassFrontmatter":true}
---



### OceanBase 数据库 Oracle 租户至 Oracle 数据库的数据迁移
迁移 【`OceanBase 数据库 Oracle 租户`】的数据至 【`Oracle 数据库`】时，OMS 会对无主键表添加隐藏列和唯一索引；

【`OceanBase 数据库 Oracle 租户`】至【 `Oracle 数据库`】的数据迁移，OMS 会自动添加隐藏列至 `CREATE TABLE` 语句中；
```sql
//Oracle 数据库 12C 以前版本
CREATE TABLE "table3" (
    "id" NUMBER,
    "status" VARCHAR2(1 BYTE),
    "OMS_PK_INCRMT" NUMBER
);

//Oracle 数据库 12C 及以后版本
CREATE TABLE "table3" (
    "id" NUMBER,
    "status" VARCHAR2(1 BYTE),
    "OMS_PK_INCRMT" NUMBER INVISIBLE
);
```


#### 1 ####
您可以执行下述命令（其中 {schema}、{table} 需要替换为实际的库名和表名），判断是否为无主键表。如果无返回结果，即可视为无主键表；

```sql
SELECT 1 FROM ( 
	SELECT DC.OWNER, DC.TABLE_NAME, DC.CONSTRAINT_NAME 
	FROM ALL_CONS_COLUMNS DCC 
	JOIN ALL_CONSTRAINTS DC ON DCC.CONSTRAINT_NAME = DC.CONSTRAINT_NAME AND DCC.OWNER = DC.OWNER 
	JOIN ALL_TAB_COLUMNS DTC ON DCC.COLUMN_NAME = DTC.COLUMN_NAME AND DCC.OWNER = DTC.OWNER AND DCC.TABLE_NAME = DTC.TABLE_NAME 
WHERE DCC.OWNER = {schema} AND DCC.TABLE_NAME = {table} AND DC.CONSTRAINT_TYPE IN ('U', 'P') 
GROUP BY DC.OWNER, DC.TABLE_NAME, DC.CONSTRAINT_NAME 
HAVING COUNT(*) = COUNT( CASE DTC.NULLABLE WHEN 'Y' THEN NULL ELSE 1 END ) 
MINUS 
SELECT TABLE_OWNER, TABLE_NAME, INDEX_NAME FROM ALL_IND_EXPRESSIONS WHERE TABLE_OWNER = {schema} AND TABLE_NAME = {table} );
```

- 添加，删除隐藏列
```sql
-- 添加隐藏列
	-- Oracle 数据库 12C 之前版本
	ALTER TABLE "{schema}"."{table}" ADD "OMS_PK_INCRMT" NUMBER;
	-- Oracle 数据库 12C 及之后版本
	ALTER TABLE "{schema}"."{table}" ADD "OMS_PK_INCRMT" NUMBER INVISIBLE;

-- 删除隐藏列
	ALTER TABLE "{schema}"."{table}" DROP COLUMN "OMS_PK_INCRMT";
```

- 添加，删除唯一索引
```sql
-- 添加唯一索引
	-- 有分区字段
	CREATE UNIQUE INDEX "{schema}"."{table}" ON "{schema}"."{table}"("{partition_col_0}", "{partition_col_1}", "OMS_PK_INCRMT") LOCAL;
	-- 没有分区字段
	CREATE UNIQUE INDEX "{schema}"."{table}" ON "{schema}"."{table}"("OMS_PK_INCRMT");

-- 删除唯一索引
	DROP INDEX "{schema}"."{table}";
```


### 参考文档

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">



### 参考文档
1. *数据迁移服务隐藏列机制说明*：[4.2.3](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000988071)，；




</div></div>



