---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 创建函数索引_MySql 模式/","dgPassFrontmatter":true}
---


### OceanBase 创建函数索引_MySql 模式

基于表中一列或多列的值进行计算后的结果建立的索引称为`函数索引`；函数索引是一种优化技术，使用函数索引可以在查询时快速定位匹配的函数值，从而避免重复计算，提高查询效率；

#### 1 创建索引注意事项

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/oracle/oracle/#55df93" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



> [!NOTE] `创建索引的限制`：
> 1. 在 OceanBase 数据库中，索引名称必须在表的范围内保证唯一；
> 2. 索引名称的长度不能超过 128 字节；
> 3. 唯一索引可以有多个，但其列值必须唯一；
> 4. 如果要使用局部唯一索引去对数据唯一性做约束，那么局部唯一索引中必须包含表分区键；

</div></div>


##### 1.1 函数索引使用限制
在 OceanBase 数据库的 MySQL 模式中，对函数索引的表达式进行了限制，禁止部分系统函数的表达式作为函数索引，具体的函数列表请参见 [函数索引支持的系统函数列表](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220209) 和 [函数索引不支持的系统函数列表](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220207)，；

#### 2 创建函数索引
基于表中一列或多列的值进行计算后的结果建立的索引称为`函数索引`；函数索引是一种优化技术，使用函数索引可以在查询时快速定位匹配的函数值，从而避免重复计算，提高查询效率；

##### 2.1 使用 CREATE TABLE 语句创建函数索引
可以在创建表时即为表创建函数索引；

```sql
-- 语法如下：
	CREATE TABLE table_name({column_name column_definition,[column_name column_definition,...]} [UNIQUE| SPATIAL] {INDEX|KEY} [index_name](expr));
/* 相关参数说明如下：
	`table_name`：指定待创建索引的表的表名。
	`column_definition`：定义表中各列的数据类型。
	`[UNIQUE | SPATIAL]`：可选，`UNIQUE` 表示唯一索引；`SPATIAL` 表示空间索引。创建唯一索引或空间索引时需要添加对应的关键字。
    `INDEX|KEY`：表示在该语句中，索引关键字使用 `INDEX` 或 `KEY` 都可以。
	`index_name`：可选，待创建的函数索引的名称。如果不指定，则系统会自动生成一个名称。格式为 `functional_index_xx`，`xx` 为索引编号。
    `expr`：合法的函数索引表达式，且允许是布尔表达式，例如 `c1=c1`；
*/

-- 创建函数索引 `tbl1_func_idx1` 的示例如下：
CREATE TABLE tbl1_func (c1 int, c2 int, index tbl1_func_idx1 ((c1+1)), UNIQUE KEY ((c1+c2)));

-- 创建成功后，可以通过 `SHOW CREATE TABLE` 语句查看建表语句，语句如下：
SHOW CREATE TABLE tbl1_func;
+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table     | Create Table                                                                                                                                                                                                                                                                                                                                                                                        |
+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| tbl1_func | CREATE TABLE `tbl1_func` (
  `c1` int(11) DEFAULT NULL,
  `c2` int(11) DEFAULT NULL,
  UNIQUE KEY `functional_index` ((`c1` + `c2`)) BLOCK_SIZE 16384 LOCAL,
  KEY `tbl1_func_idx1` ((`c1` + 1)) BLOCK_SIZE 16384 LOCAL
) DEFAULT CHARSET = utf8mb4 ROW_FORMAT = DYNAMIC COMPRESSION = 'zstd_1.3.8' REPLICA_NUM = 1 BLOCK_SIZE = 16384 USE_BLOOM_FILTER = FALSE TABLET_SIZE = 134217728 PCTFREE = 0 |
+-----------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set
```


##### 2.2 使用 CREATE INDEX 语句创建函数索引

表创建成功后，可以为已有表创建函数索引；

```sql
-- 语法如下：
	CREATE [UNIQUE | SPATIAL] INDEX index_name ON table_name (expr);
/*
相关参数说明如下：
	`[UNIQUE | SPATIAL]`：可选，`UNIQUE` 表示唯一索引；`SPATIAL` 表示空间索引。创建唯一索引或空间索引时需要添加对应的关键字。
    `index_name`：待创建的函数索引的名称。
    `table_name`：待创建索引的表。
    `expr`：合法的函数索引表达式，且允许是布尔表达式，例如 `c1=c1`。
*/

```


    

在表上创建一个索引定义为 `c1+c2 < 1` 的函数索引 `tbl2_func_idx1`，示例如下：
```sql
-- 1. 创建表 `tbl2_func`
CREATE TABLE tbl2_func(c1 int, c2 int);

-- 2. 创建函数索引 `tbl2_func_idx1`
CREATE INDEX tbl2_func_idx1 on tbl2_func ((c1+c2 < 1));

-- 3. 创建成功后，可以查看创建的函数索引
SHOW INDEX FROM tbl2_func;
+-----------+------------+----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+-----------+---------------+---------+---------------------+
| Table     | Non_unique | Key_name       | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment   | Index_comment | Visible | Expression          |
+-----------+------------+----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+-----------+---------------+---------+---------------------+
| tbl2_func |          1 | tbl2_func_idx1 |            1 | SYS_NC18$   | A         |        NULL | NULL     | NULL   | YES  | BTREE      | available |               | YES     | ((`c1` + `c2`) < 1) |
+-----------+------------+----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+-----------+---------------+---------+---------------------+
1 row in set
```


##### 2.3 使用 ALTER TABLE 语句创建函数索引

```sql
-- 语法如下：
	ALTER TABLE table_name ADD [UNIQUE | SPATIAL] {INDEX | KEY} [index_name](expr);
/* 相关参数说明如下：
	`table_name`：待创建索引的表。
    `[UNIQUE | SPATIAL]`：可选，`UNIQUE` 表示唯一索引；`SPATIAL` 表示空间索引。创建唯一索引或空间索引时需要添加对应的关键字。
    `INDEX|KEY`：表示在该语句中，索引关键字使用 `INDEX` 或 `KEY` 都可以。
    `index_name`：可选，待创建的函数索引的名称。如果不指定，则系统会自动生成一个名称，且名称格式为 `functional_index` 前缀加编号。
    `expr`：合法的函数索引表达式，且允许是布尔表达式，例如 `c1=c1`。
*/
```


 
```sql
-- 1.创建表 `tbl3_func`
CREATE TABLE tbl3_func(c1 int, c2 int);

-- 2.为表创建 2 个函数索引
 ALTER TABLE tbl3_func ADD INDEX ((c1+1));
 ALTER TABLE tbl3_func ADD INDEX ((concat(c1,'a')));

-- 3.查看索引信息
SHOW INDEX FROM tbl3_func;
+-----------+------------+--------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+-----------+---------------+---------+------------------+
| Table     | Non_unique | Key_name           | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment   | Index_comment | Visible | Expression       |
+-----------+------------+--------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+-----------+---------------+---------+------------------+
| tbl3_func |          1 | functional_index   |            1 | SYS_NC18$   | A         |        NULL | NULL     | NULL   | YES  | BTREE      | available |               | YES     | (`c1` + 1)       |
| tbl3_func |          1 | functional_index_2 |            1 | SYS_NC19$   | A         |        NULL | NULL     | NULL   | YES  | BTREE      | available |               | YES     | concat(`c1`,'a') |
+-----------+------------+--------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+-----------+---------------+---------+------------------+
2 rows in set
```


### 参考文档

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/my-sql/ocean-base/#830d2c" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



2. `创建和管理索引` 

</div></div>



