---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/OMS 迁移数据/OMS 架构及原理/OceanBase 数据库 Oracle 租户之间的数据迁移/","dgPassFrontmatter":true}
---



### OceanBase 数据库 Oracle 租户之间的数据迁移

> [!NOTE] 注意事项：
> *容灾双活场景仅支持迁移有唯一键表*；

迁移 【`OceanBase 数据库 Oracle 租户`】的数据至 `OceanBase 数据库 Oracle 租户`时，OMS 会对*无主键表* 添加【`隐藏列`】和【`唯一索引`】；

OceanBase 数据库 Oracle 租户之间的数据迁移，OMS 会自动添加隐藏列至 `CREATE TABLE` 语句中；

```sql
CREATE TABLE "table4" (
    "V1" CHAR(20 BYTE) DEFAULT 'test',
    "OMS_PK_INCRMT" NUMBER
);
```


#### 1 使用示例
创建表
```sql
-- 创建表
CREATE TABLE "test_0906_001" ("ID" int);  
CREATE TABLE "test_0906_002" ("ID" int, primary key("ID"));  

-- 查询表结构
SHOW CREATE TABLE "test_0906_001";  
SHOW CREATE TABLE "test_0906_002";  
```

判断是否为无主键表。如果无返回结果，即可视为无主键表；

```sql
-- 判断是否为无主键表。如果无返回结果，即可视为无主键表
SELECT 1 FROM (  
	SELECT DC.OWNER, DC.TABLE_NAME, DC.CONSTRAINT_NAME  
	FROM ALL_CONS_COLUMNS DCC  
	JOIN ALL_CONSTRAINTS DC ON DCC.CONSTRAINT_NAME = DC.CONSTRAINT_NAME AND DCC.OWNER = DC.OWNER  
	JOIN ALL_TAB_COLUMNS DTC ON DCC.COLUMN_NAME = DTC.COLUMN_NAME AND DCC.OWNER = DTC.OWNER AND DCC.TABLE_NAME = DTC.TABLE_NAME  
	WHERE DCC.OWNER = 'SYS' AND DCC.TABLE_NAME = 'test_20240906' AND DC.CONSTRAINT_TYPE IN ('U', 'P')  
	GROUP BY DC.OWNER, DC.TABLE_NAME, DC.CONSTRAINT_NAME  
	HAVING COUNT(*) = COUNT( CASE DTC.NULLABLE WHEN 'Y' THEN NULL ELSE 1 END ) 
	MINUS  
	SELECT TABLE_OWNER, TABLE_NAME, INDEX_NAME FROM ALL_IND_EXPRESSIONS WHERE TABLE_OWNER = 'SYS' AND TABLE_NAME = 'test_20240906'  
);  
  
SELECT 1 FROM (  
SELECT DC.OWNER, DC.TABLE_NAME, DC.CONSTRAINT_NAME  
FROM ALL_CONS_COLUMNS DCC  
JOIN ALL_CONSTRAINTS DC ON DCC.CONSTRAINT_NAME = DC.CONSTRAINT_NAME AND DCC.OWNER = DC.OWNER  
JOIN ALL_TAB_COLUMNS DTC ON DCC.COLUMN_NAME = DTC.COLUMN_NAME AND DCC.OWNER = DTC.OWNER AND DCC.TABLE_NAME = DTC.TABLE_NAME  
WHERE DCC.OWNER = 'SYS' AND DCC.TABLE_NAME = 'test_0906_002' AND DC.CONSTRAINT_TYPE IN ('U', 'P')  
GROUP BY DC.OWNER, DC.TABLE_NAME, DC.CONSTRAINT_NAME  
HAVING COUNT(*) = COUNT( CASE DTC.NULLABLE WHEN 'Y' THEN NULL ELSE 1 END )  
MINUS  
SELECT TABLE_OWNER, TABLE_NAME, INDEX_NAME FROM ALL_IND_EXPRESSIONS WHERE TABLE_OWNER = 'SYS' AND TABLE_NAME = 'test_0906_002'  
);  
  
```

添加，删除隐藏列  
```sql
-- 1.添加，删除隐藏列  
-- 添加隐藏列  
	-- 语法：ALTER TABLE "{schema}"."{table}" ADD "OMS_PK_INCRMT" NUMBER INVISIBLE;  
	ALTER TABLE "test_0906_001" ADD "OMS_PK_INCRMT" NUMBER INVISIBLE;  

-- 删除隐藏列  
	-- 语法：ALTER TABLE "{schema}"."{table}" DROP COLUMN "OMS_PK_INCRMT";  
	ALTER TABLE "SYS"."test_0906_001" DROP COLUMN "OMS_PK_INCRMT";
```

添加，删除唯一索引
```sql
-- 2.添加，删除唯一索引
-- 添加唯一索引  
/* 语法：
	-- 有分区字段  
	CREATE UNIQUE INDEX "{schema}"."UK_{table}_OBPK_INCRMT" ON "{schema}"."{table}"("{partition_col_0}", "{partition_col_1}", "OMS_PK_INCRMT") LOCAL;  
	-- 没有分区字段  
	CREATE UNIQUE INDEX "{schema}"."UK_{table}_OBPK_INCRMT" ON "{schema}"."{table}"("OMS_PK_INCRMT");  
*/
CREATE UNIQUE INDEX "UK_test_0906_001_OBPK_INCRMT" ON "test_0906_001"("OMS_PK_INCRMT");  


-- 删除唯一索引  
-- 语法：DROP INDEX "{schema}"."UK_{table}_OBPK_INCRMT";  
DROP INDEX "SYS"."UK_test_0906_001_OBPK_INCRMT";  
```


### 参考文档

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">



### 参考文档
1. *数据迁移服务隐藏列机制说明*：[4.2.3](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000988071)，；




</div></div>



