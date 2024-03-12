---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理索引_Oracle 租户/","dgPassFrontmatter":true}
---


### 管理索引_Oracle 租户
#### 1 查看索引  
可以通过系统视图来查看索引，查看索引的视图包括 [USER_INDEXES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000508842)，[ALL_INDEXES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000508915),[DBA_INDEXES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000508887)，`ALL_IND_COLUMNS`、`DBA_IND_COLUMNS` 和 `USER_IND_COLUMNS`；  

```sql
-- 1.
	-- USER_INDEXES：用于查看用户拥有的所有表的索引信息；查看用户拥有的所有表的索引信息
	SELECT * FROM USER_INDEXES WHERE table_name='TEST';  
	
	-- ALL_INDEXES: 用于查看用户可访问的所有表的索引的索引列信息；查看表的所有索引
	SELECT * FROM ALL_INDEXES WHERE table_name='TEST';  
	SELECT OWNER,INDEX_NAME,INDEX_TYPE,TABLE_OWNER,TABLE_NAME,TABLE_TYPE,UNIQUENESS,COMPRESSION,STATUS,PARTITIONED,VISIBILITY 
	FROM ALL_INDEXES WHERE table_name='TEST';
	  
	-- DBA_INDEXES：用于查看数据库所有表的索引信息；
	SELECT * FROM DBA_INDEXES; 
	SELECT OWNER,INDEX_NAME,INDEX_TYPE,TABLE_OWNER,TABLE_NAME,TABLE_TYPE,UNIQUENESS,COMPRESSION,STATUS,PARTITIONED,VISIBILITY 
	FROM DBA_INDEXES WHERE table_name='TEST';

-- 2. 查询索引列信息
	-- USER_IND_COLUMNS：查看用户拥有的所有表的索引的索引列信息；查看表的索引的详细信息  
	SELECT * FROM USER_IND_COLUMNS WHERE table_name='TEST' ORDER BY INDEX_NAME, COLUMN_POSITION; 
	
	-- ALL_IND_COLUMNS：查看用户可访问的所有表的索引的索引列信息；  
	SELECT * FROM ALL_IND_COLUMNS WHERE table_name='AK_YJJEB' ORDER BY INDEX_NAME, COLUMN_POSITION;  
	  
	-- DBA_IND_COLUMNS：查看数据库所有表的索引的索引列信息；  
	SELECT * FROM DBA_IND_COLUMNS WHERE table_name='AK_YJJEB' ORDER BY INDEX_NAME, COLUMN_POSITION;   
```


#### 2 创建索引  

#### 3 删除索引  
```sql
-- 删除索引  
DROP INDEX t3_ind2;
```



### 参考文档



