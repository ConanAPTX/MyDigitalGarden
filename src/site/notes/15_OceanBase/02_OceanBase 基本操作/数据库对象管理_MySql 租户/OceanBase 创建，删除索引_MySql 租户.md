---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 创建，删除索引_MySql 租户/","dgPassFrontmatter":true}
---


### OceanBase 创建，删除索引_MySql 租户

#### 1 删除索引  
当索引过多时，维护开销会增大，您可以根据需要删除不必要的索引；  


##### 1.1 使用 `ALTER TABLE` 语句删除索引

```sql
-- 删除索引
/* 语法如下：
	ALTER TABLE table_name DROP KEY | INDEX index_name;
参数：
	`table_name`：指定待删除索引的表的表名。  
	`KEY|INDEX`：表示在该语句中，索引关键字使用 `INDEX` 或 `KEY` 都可以。  
	`index_name`：指定待删除的索引的索引名。
*/

-- 使用 `ALTER TABLE` 语句删除表的索引
obclient> ALTER TABLE t3 DROP KEY t3_uk, DROP KEY t3_ind3;
obclient> ALTER TABLE t3 DROP KEY t3_uk, DROP KEY t3_ind3;
Query OK, 0 rows affected
```

##### 1.2 使用 `DROP INDEX` 语句删除索引

语法如下：

```sql
/* 语法如下：
	DROP INDEX index_name ON table_name;
参数：
	`index_name`：指定待删除的索引的索引名。
	`table_name`：指定待删除索引的表的表名。
*/

-- 使用 `DROP INDEX` 语句删除索引
obclient> drop index index_id on tab_1;    -- 删除 tab_1 表的 index_id 索引
obclient> DROP INDEX t3_ind3 ON t3;
Query OK, 0 rows affected
```


#### 2 创建索引  
##### 2.1 创建普通索引
可以使用 `CREATE TABLE` 语句、`CREATE INDEX` 语句或 `ALTER TABLE` 语句来创建普通索引；

###### 2.1.1 使用 CREATE TABLE 语句创建普通索引

可以通过 `CREATE TABLE` 语句在创建表时即为表创建索引；
```sql
-- 语法如下：
	CREATE TABLE table_name(column_name column_definition,[column_name column_definition,...] [UNIQUE] INDEX|KEY [index_name](column_name));
/*
相关参数说明如下：
	`table_name`：指定待创建的表的表名。
    `column_name`：指定表的列。
    `column_definition`：定义表中各列对应的数据类型。
    `UNIQUE`：可选，表示唯一索引。创建唯一索引时需要添加该关键字。
    `INDEX|KEY`：表示在该语句中，索引关键字使用 `INDEX` 或 `KEY` 都可以。
    `index_name`：可选，指定待创建的索引的索引名。如果未指定，默认索引名与指定的列名相同。
*/

-- 创建表 `tbl1`，同时创建索引 `tbl1_idx1` 的示例如下：
CREATE TABLE tbl1(id INT,name VARCHAR(18),date DATE,PRIMARY KEY (id),INDEX tbl1_idx1 (date));
```


###### 2.1.2 使用 CREATE INDEX 语句创建普通索引
可以通过 `CREATE INDEX` 语句为已有的表创建索引；

```sql
-- 语法如下：
	CREATE [UNIQUE] INDEX index_name ON table_name (column_name);
	CREATE [UNIQUE] INDEX index_name ON table_name ( column_list ) [LOCAL | GLOBAL]  [ PARTITION BY column_list PARTITIONS N ];  
/*
相关参数说明如下：
	`UNIQUE`：可选，表示唯一索引。创建唯一索引时需要添加该关键字。
    `index_name`：指定待添加的索引的索引名。
    `table_name` ：表名，指定待创建索引的表的表名。
    column_name：指定对哪些列进行索引，指定多列时，各列之间用英文逗号分隔。  
	LOCAL：局部索引。MySQL 模式中在创建索引时如果不显式指定 GLOBAL 或 LOCAL ，则默认是 LOCAL；  
	GLOBAL：全局索引；  
	PARTITION BY column_list PARTITIONS N ：指定分区方式及类型； 
*/

-- 1.创建 tbl2 表后，再创建索引 tbl2_idx1
CREATE TABLE tbl2(id INT,name VARCHAR(20));   -- 创建表 `tbl2`
CREATE INDEX tbl2_idx1 ON tbl2(id);           -- 在表 tbl2 上创建索引 tbl2_idx1

-- 2.
CREATE TABLE tab_1 (c1 int primary key, c2 VARCHAR(10)); -- 创建表  
CREATE INDEX idx ON tab_1 (c1, c2);                      -- 为非分区表创建普通索引 
```


###### 2.1.3 使用 ALTER TABLE 语句创建普通索引
可以通过 `ALTER TABLE` 语句为已存在的表创建索引，且该语句支持一次增加多个索引；

OB 数据库支持在创建表后为表增加唯一索引。如果创建表时同时设置了主键，OB 数据库会默认为主键列创建一个唯一索引；

```sql
-- 语法如下：
	ALTER TABLE table_name ADD [UNIQUE] INDEX|KEY [index_name](column_name);
/* 相关参数说明如下：
	table_name：指定待创建索引的表的表名。
    UNIQUE：可选，表示唯一索引。创建唯一索引时需要添加该关键字。
    INDEX|KEY：表示在该语句中，索引关键字使用 INDEX 或 KEY 都可以；
    index_name：可选，指定待创建的索引的索引名。如果未指定，默认索引名与指定的列名相同。
    column_name：指定对哪些列进行索引，指定多列时，各列之间用英文逗号分隔。
*/

-- 1.创建表 tbl3 后，在创建索引 tbl3_idx1 
CREATE TABLE tbl3(id INT,name VARCHAR(20));   -- 创建表 tbl3；
ALTER TABLE tbl3 ADD INDEX tbl3_idx1(id);     -- 为表 tbl3 创建索引 tbl3_idx1

-- 2.增加普通索引
CREATE TABLE test (c1 int PRIMARY KEY, c2 VARCHAR(50));  
ALTER TABLE test ADD INDEX myidx(c1,c2);  

-- 3.增加唯一索引
CREATE TABLE test (c1 int PRIMARY KEY, c2 VARCHAR(50));  
ALTER TABLE test ADD UNIQUE INDEX index_name(c2);  
```

##### 2.2 创建空间索引
[[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 创建空间索引_MySql 模式\|OceanBase 创建空间索引_MySql 模式]]，；
 

##### 2.3 创建函数索引
[[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 创建函数索引_MySql 模式\|OceanBase 创建函数索引_MySql 模式]]，；

### 参考文档

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/my-sql/ocean-base/#830d2c" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



2. `创建和管理索引` 

</div></div>














