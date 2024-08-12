---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理表_Oracle 租户/","dgPassFrontmatter":true}
---


### 管理表_Oracle 租户
#### 1 创建，删除表  
```sql  
-- 1.创建表  
CREATE TABLE test (c1 int PRIMARY KEY, c2 VARCHAR(50));  -- 直接创建表
CREATE TABLE t2_copy AS SELECT * FROM t2;                -- 复制已有表的数据创建新表

-- 2.删除表
DROP TABLE test; -- 删除表  
```  

Oracle 租户删除表详细情况，[删除表_Oracle_3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355498)，；  


#### 4 修改表  
##### 4.1 修改表名  
```sql  
CREATE TABLE test (c1 int PRIMARY KEY, c2 VARCHAR(50)); -- 创建表  
  
-- 修改表名  
ALTER TABLE test RENAME TO t1; -- 将表名 test 修改为 t1  
RENAME TABLE test TO t1; -- 将表名 test 修改为 t1  
  
DESC test_tbl1; -- 查询表结构信息  
```  

##### 4.2 修改表结构  
OceanBase 数据库 3.2.3 版本修改列类型时，仅支持增加特定字符数据类型（VARCHAR、VARBINARY、CHAR 等）的长度；OceanBase 数据库 3.2.4 版本当前版本只支持扩展字段长度，不支持修改字段类型；  
```sql  
CREATE TABLE test (c1 int PRIMARY KEY, c2 VARCHAR(50)); -- 创建表  
  
-- 修改列名  
ALTER TABLE test RENAME COLUMN c1 TO name; -- 将 test 表中列 col2 的名称改成 name  
DESC USER1.test; -- 查询表字段信息  
  
-- 修改列类型，默认值，非空约束  
ALTER TABLE test MODIFY (c2 VARCHAR2(70));  
-- OceanBase 3.2.4 版本只支持扩展字段长度，不支持修改字段类型；  
ALTER TABLE test MODIFY c1 DEFAULT 1; -- 修改列的默认值  
ALTER TABLE test MODIFY (c2 NOT NULL); -- 修改列的非空约束  
DESC USER1.test; -- 查询表字段信息  
  
-- 唯一约束  
CREATE TABLE test_1 (c1 int PRIMARY KEY, c2 VARCHAR(50));  
ALTER TABLE test_1 ADD UNIQUE(c2); -- 增加唯一约束会在该表增加唯一索引  
  
-- 增加，删除列  
ALTER TABLE test ADD c3 int; -- 在 test 表中，增加 c3 列  
ALTER TABLE test DROP COLUMN c3; -- 在 test 表中，删除 c3 列  
DESC USER1.test; -- 查询表字段信息  
  
-- 添加，删除表的外键  
ALTER TABLE test ADD CONSTRAINT fk_name FOREIGN KEY (gid) REFERENCES test1(id);  
ALTER TABLE test DROP CONSTRAINT fk_name;  
```  
更多详细情况：[关于表结构的修改_3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945635)，[更改表_Oracle_3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355496)，；  

##### 4.3 更改表的主键  
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
-- Oracle 租户语法：ALTER TABLE table_name MODIFY PRIMARY KEY (column_name);  
ALTER TABLE tbl1 MODIFY PRIMARY KEY (c2);  
  
-- 删除主键  
-- 语法：ALTER TABLE table_name DROP PRIMARY KEY;  
ALTER TABLE tbl1 DROP PRIMARY KEY;  
```  
详细情况：[主键约束_Oracle_3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000357192)，[主键操作_Oracle_4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000884610)，；  

##### 4.4 更改表的副本数  
```sql  
ALTER TABLE test SET REPLICA_NUM=2; -- 设置表的副本数为 2  
```


### 参考文档



