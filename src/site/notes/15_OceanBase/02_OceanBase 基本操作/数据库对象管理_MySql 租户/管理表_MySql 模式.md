---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/管理表_MySql 模式/","dgPassFrontmatter":true}
---


### 管理表_MySql 模式
#### 1 查询表结构，表定义  
```sql  
-- 1.查询表的定义
DESC test;       -- 查询表结构  
DESCRIBE test;   -- 查询表结构  

-- 2.查询表的定义
SHOW CREATE TABLE test;  -- 查询表的定义
/*  
DEFAULT CHARSET = utf8mb4 指定默认字符集为 utf8mb4；  
ROW_FORMAT = DYNAMIC 表示这张表为动态表，若一张表里面存在 varchar、text 以及其变形、blob以及其变形的字段的话，那么这张表其实也叫动态表，
	即该表的 row_format 是 dynamic；该表 row_format 是 fixed 那么张这个表是静态表。  
COMPRESSION 该字段信息显示的是表压缩方式；  
PCTFREE 字段信息表示留给更新该块数据使用空间；  
*/
```  


#### 2 创建，删除表
##### 2.1 创建表
###### 2.1.1 创建非分区表  
###### 2.1.2 创建复制表  
复制表是 OceanBase 数据库的高级优化手段；  
通常 OceanBase 集群是三副本架构，默认每个表的每个分区在 OceanBase 数据库中会有三个副本数据，在角色上分为一个主副本（Leader 副本）和两个备副本（Follower 副本），默认由主副本提供读写服务；  
复制表可以在指定租户的每台机器上都有一个备副本，并且主副本与所有备份的数据使用全同步策略保持强同步。这样做的目的是为了让业务有些 SQL 关联查询时能在同一节点内部执行，以获取更好的性能； 

```sql  
CREATE TABLE table_name (i_id int, i_name varchar(24), i_price decimal(5,2), i_data varchar(50), i_im_id int
	, primary key(i_id)) COMPRESS FOR QUERY pctfree=0 BLOCK_SIZE=16384  
	duplicate_scope='cluster' locality='F,R{all_server}@doc_1, F,R{all_server}@doc_2,F,R{all_server}@doc_3' primary_zone='doc_1';  
```  

###### 2.1.3 复制已有表的数据创建新表  
使用 `CREATE TABLE AS SELECT` 语句复制表的数据，但是结构并不完全一致，并且会丢失约束、索引、默认值、分区等信息；
可以使用 `CREATE TABLE LIKE` 语句复制表结构，但是不能复制表数据；

```sql  
-- 3.复制已有表的数据创建新表
	-- 使用 `CREATE TABLE AS SELECT` 语句复制表的数据，但是结构并不完全一致，并且会丢失约束、索引、默认值、分区等信息；
	CREATE TABLE t1_copy AS SELECT * FROM t1; -- 复制表数据  
	
	-- 可以使用 `CREATE TABLE LIKE` 语句复制表结构，但是不能复制表数据；
	CREATE TABLE t1_like like t1; -- 复制表结构  
```  


##### 2.2 删除表  
当一个表不再使用时，可以使用 `DROP TABLE` 语句将其删除，对于 `DROP TABLE` 操作：当租户关闭回收站功能时，`DROP TABLE` 操作表示删除表；当租户开启回收站功能时，`DROP TABLE` 操作表示删除的表会进入回收站；  

```sql  
-- 删除表  
DROP TABLE test;  
DROP TABLE IF EXISTS test;  
```


#### 3 修改表名，表结构  
##### 3.1 重命名表  
```sql  
CREATE TABLE tbl1(col1 INT,col2 INT,col3 VARCHAR(50)); -- 创建表  
/*  
语法：  
ALTER TABLE table_name RENAME new_table_name;  
RENAME TABLE table_name TO new_table_name;  
*/  
ALTER TABLE tbl1 RENAME test_tbl1; -- 修改表名  
ALTER TABLE test RENAME TO t1; -- 修改表名  
RENAME TABLE test TO t1; -- 修改表名  
  
SHOW TABLES;  
```  

##### 3.2 修改表结构  
OceanBase 数据库 3.2.3 版本修改列名和列定义，仅支持增加特定字符数据类型（VARCHAR、VARBINARY、CHAR 等）的长度；  
```sql  
CREATE TABLE tbl1(col1 INT,col2 INT,col3 VARCHAR(50)); -- 创建表  
  
-- 修改列名  
ALTER TABLE test_tbl1 CHANGE col3 name VARCHAR(50);  
  
-- 修改列类型  
-- 语法：ALTER TABLE table_name MODIFY[COLUMN] new_column_name_type;  
ALTER TABLE test_tbl1 MODIFY col2 INT(18); -- 将 col2 列的数据类型从 INT(11) 转换为 INT(18)  
DESC test_tbl1;  
  
-- 增加,删除列  
/* 语法：  
ALTER TABLE table_name ADD[COLUMN] column_name column_type; -- 增加列  
ALTER TABLE table_name DROP[COLUMN] column_name; -- 删除列  
*/  
-- 新增列，支持在指定位置新增列  
ALTER TABLE test_tbl1 ADD col4 char(1); -- 在 test_tbl1 表中，增加 col4 列  
ALTER TABLE test ADD c3 int; -- 新增列  
ALTER TABLE test ADD COLUMN c4 INT NULL AFTER c1; -- 在 c1 列后面指定添 c4 列  
ALTER TABLE test ADD COLUMN c6 INT NULL BEFORE c1; -- 在 c1 列前面指定添 c6 列  
ALTER TABLE test_tbl1 DROP col4; -- 在 test_tbl1 表中，删除 col4 列；  
-- OceanBase 2.x，3.x 版本不支持增加主键列；  
-- OceanBase 3.2.3 版本删除列时，不允许删除主键列或者包含索引及非空的列；  
DESC test_tbl1;  
  
-- 修改列属性，支持修改列名称和列类型  
ALTER TABLE test CHANGE COLUMN c2 c CHAR(60);  
ALTER TABLE test CHANGE COLUMN c2 c2 varchar(50) DEFAULT 2; --  
```  
更多详细情况：[关于修改表结构_3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000357142)，；  

##### 3.3 更改表的主键  
OceanBase 数据库 2.x，3.x 版本只支持在建表时通过 CREATE TABLE 创建主键约束，暂不支持通过 ALTER TABLE 追加、删除、修改主键约束；  

```sql  
-- 创建表时添加外键  
CREATE TABLE t1(c1 INT, c2 INT, CONSTRAINT pk_c1_c2 PRIMARY KEY(c1, c2));  
```  
OceanBase 数据库 4.x 版本的主键操作包括添加主键、修改主键和删除主键，才开始支持通过 **ALTER TABLE** 追加、删除、修改主键约束；  
```sql  
-- 添加主键  
-- 语法：ALTER TABLE table_name ADD PRIMARY KEY (column_name);  
CREATE TABLE tbl1(c1 INT,c2 VARCHAR(50)); -- 创建表  
ALTER TABLE tbl1 ADD PRIMARY KEY(c1); -- 添加主键  
  
-- 修改主键  
-- MySQL 租户语法：ALTER TABLE table_name DROP PRIMARY KEY,ADD PRIMARY KEY (column_name_list);  
ALTER TABLE tbl1 DROP PRIMARY KEY,ADD PRIMARY KEY(c2);  
  
-- 删除主键  
-- 语法：ALTER TABLE table_name DROP PRIMARY KEY;  
ALTER TABLE tbl1 DROP PRIMARY KEY;  
```  
详细情况：[主键约束_MySql_3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000357182)，[主键操作_MySql_4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000884759)，；  

### 参考文档：
