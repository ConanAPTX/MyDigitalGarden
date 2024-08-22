---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/查询表的信息_Oracle 模式/","dgPassFrontmatter":true}
---



### 文档名称

#### 1 查询创建表语句
```sql  
-- 1.查询表字段信息
	DESC USER1.test;     -- 查询表字段信息  
	DESCRIBE USER1.test; -- 查询表字段信息  

-- 查询表创建语句
	-- 查询表的创建语句
	SHOW CREATE TABLE USER1.test; -- 查询表的定义
	/*  
		DEFAULT CHARSET = utf8mb4 指定默认字符集为 utf8mb4；  
		ROW_FORMAT = DYNAMIC 表示这张表为动态表，若一张表里面存在 varchar、text 以及其变形、blob以及其变形的字段的话，那么这张表其实也叫动态表，
			即该表的 row_format 是 dynamic；该表 row_format 是 fixed 那么张这个表是静态表。  
		COMPRESSION 该字段信息显示的是表压缩方式；  
		PCTFREE 字段信息表示留给更新该块数据使用空间；  
	*/

	-- 查询表创建语句  
	SELECT dbms_metadata.get_ddl('TABLE','TEST','USER1') FROM DUAL;  
		-- TABLE：；TEST：表名；SYS：代表查看 SYS 用户下的表；
```  
查看表的定义详细情况：[查看表的定义](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355495)，；  

#### 2 查询所有表信息
*查询所有表信息*：`DBA_TABLES >= ALL_TABLES >= USER_TABLES` ；
```sql  
-- DBA_TABLES：DBA拥有的或者可以访问的所有关系表  
SELECT owner, table_name FROM DBA_TABLES ORDER BY owner, table_name;  
  
-- ALL_TABLES：某一用户所拥有的或者可以访问的所有关系表  
SELECT owner, table_name FROM all_tables ORDER BY owner, table_name;  
SELECT owner, table_name, tablespace_name, avg_row_len, num_rows, last_analyzed FROM all_tables ORDER BY owner, table_name;  
  
-- USER_TABLES：某一用户所拥有的所有关系表  
SELECT table_name FROM user_tables ORDER BY table_name; -- 查询所有表的名称  
SELECT * FROM user_tables where table_name = upper('ali_org');  
```  
关于视图更多信息：[[15_OceanBase/99_内部表介绍/表和视图相关_oracle_01#1 USER_TABLES, ALL_TABLES, DBA_TABLES\|表和视图相关_oracle_01#1 USER_TABLES, ALL_TABLES, DBA_TABLES]]，；

```sql  
SELECT t.owner, t.table_name, c.column_name, data_type, data_length, nullable, index_name  
FROM all_tables t  
LEFT JOIN all_tab_columns c ON t.table_name = c.table_name  
LEFT JOIN all_cons_columns cc ON c.table_name = cc.table_name AND c.column_name = cc.COLUMN_NAME  
LEFT JOIN all_indexes i ON c.table_name = i.table_name  
WHERE c.owner = 'SYS'  
ORDER BY t.owner,t.table_name;  
  
SELECT c.table_name, c.column_name, data_type, data_length, nullable, index_name  
FROM all_tab_columns c  
LEFT JOIN all_cons_columns cc ON c.table_name = cc.table_name AND c.column_name = cc.COLUMN_NAME  
LEFT JOIN all_indexes i ON c.table_name = i.table_name  
WHERE c.owner = 'SYS' AND c.table_name = 'AQ$_JOBS';  
  
select * from all_cons_columns where table_name = 'AA';  
  
DESCRIBE all_cons_columns;  
DESCRIBE TEST.AA;  
```  


#### 3 查询视图的信息
关于视图更多信息：[[15_OceanBase/99_内部表介绍/表和视图相关_oracle_01#2 USER_VIEWS, ALL_VIEWS, DBA_VIEWS\|表和视图相关_oracle_01#2 USER_VIEWS, ALL_VIEWS, DBA_VIEWS]]，；

#### 4 查看表、视图的所有列；
```sql
-- 3.查看表、视图的所有列
-- 查看当前用户拥有的表、视图的所有列
SELECT * FROM USER_TAB_COLUMNS

-- 查看数据库中所有的表、视图的所有列
SELECT * FROM DBA_TAB_COLUMNS

-- 查看当前用户可访问的表、视图的所有列
SELECT * FROM ALL_TAB_COLUMNS
```
关于视图更多信息：[[15_OceanBase/99_内部表介绍/表和视图相关_oracle_01#3 查询表，视图的列信息\|表和视图相关_oracle_01#3 查询表，视图的列信息]]，；


### 参考文档



