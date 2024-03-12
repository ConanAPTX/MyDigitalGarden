---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/管理索引_MySql 租户/","dgPassFrontmatter":true}
---


### 管理索引_MySql 租户

#### 1 查看索引  

```sql
-- 查看表的索引  
SHOW INDEX FROM test; -- 查看表 test 的索引  
/*  
字段解释如下：  
	Non_unique：如果索引不能包括重复值则为0，如果可以则为1。也就是平时所说的唯一索引；  
	Key_name：；  
	Seq_in_index：索引中的列序列号，一个是1一个是2，就是表明在联合索引中的顺序；  
	Column_name：索引的列名；  
	Collation：指的是列以什么方式存储在索引中；  
	Cardinality：是基数的意思，表示索引中唯一值的数目的估计值；  
	Sub_part：前置索引的意思；  
	如果列只是被部分地编入索引，则为被编入索引的字符的数目。如果整列被编入索引，则为NULL；  
	Packed：指示关键字如何被压缩。如果没有被压缩，则为NULL；  
	Null：；  
	Index_type：表示索引类型，当前仅支持 BTREE 类型；  
*/  
```

#### 2 创建索引  
创建索引的 SQL 语法格式如下：  

```sql
/*  语法：CREATE [UNIQUE] INDEX index_name ON table_name ( column_list ) [LOCAL | GLOBAL]  [ PARTITION BY column_list PARTITIONS N ];  
字段解释如下：  
	UNIQUE：代表唯一索引；  
	table_name：表名，指需要创建索引的表；  
	column_list：指出对哪些列进行索引，多列时各列之间用逗号分隔；  
	LOCAL：局部索引。MySQL 模式中在创建索引时如果不显式指定 GLOBAL 或 LOCAL ，则默认是 LOCAL；  
	GLOBAL：全局索引；  
	PARTITION BY column_list PARTITIONS N ：指定分区方式及类型；  
*/  
CREATE TABLE tab_1 (c1 int primary key, c2 VARCHAR(10)); -- 创建表  
CREATE INDEX idx ON tab_1 (c1, c2);   -- 为非分区表创建普通索引 
```

新增索引还有一种方法，其 SQL 语法格式如下：
```sql
ALTER TABLE table_name ADD INDEX|KEY index_name ( column_list ) ;  
该语句可以一次增加多个索引，索引关键字用 INDEX 或 KEY 都可以；
```


##### 2.1 增加唯一索引  
```sql
OB 数据库支持在创建表后为表增加唯一索引。如果创建表时同时设置了主键，OB 数据库会默认为主键列创建一个唯一索引；  
CREATE TABLE test (c1 int PRIMARY KEY, c2 VARCHAR(50));  
  
ALTER TABLE test ADD UNIQUE INDEX index_name(c2);  
```


##### 2.2 增加普通索引  
```sql
OB 数据库支持一次增加多个索引，索引关键字用INDEX或KEY均可以；  
CREATE TABLE test (c1 int PRIMARY KEY, c2 VARCHAR(50));  
  
ALTER TABLE test ADD INDEX myidx(c1,c2);  
```


#### 3 删除索引  
当索引过多时，维护开销会增大，您可以根据需要删除不必要的索引；  

```sql
/* 删除索引的语法：  
ALTER TABLE table_name DROP key|index index_name ;  
DROP INDEX index_name ON table_name;  
*/  
ALTER TABLE t3 DROP KEY t3_uk, DROP KEY t3_ind3;  
drop index index_id on tab_1;                       -- 删除 tab_1 表的 index_id 索引
```



### 参考文档



