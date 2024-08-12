---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/SQL 参考，SQL 语法/NULL 值/","dgPassFrontmatter":true}
---


### NULL 值
#### 1 NULL 值_MySql 模式

`NULL` 值表示"无数据"；

`NULL` 不区分大小写。同义词是 `\N`（区分大小写）。请注意，`NULL` 值与数字 0 和空字符串 `''` 不同。

当使用 `ORDER BY` 进行排序时，对于升序排序，`NULL` 值排在其他值之前；对于降序排序，`NULL` 值排在其他值之后；

#### 2 空值_Oracle 模式
*空值*（*NULL*）指数据库表中无效的、未指定的、未知的或不可预知的值。NULL 可以存在于数据类型不受 NOT NULL 或 PRIMARY KEY 完整性约束限制的列中。任何包含 NULL 的算术表达式结果都为 NULL；

在 OceanBase Oracle 模式中，`NULL` 与 `''`（空字符串）是等价的，该行为和 Oracle 数据库是兼容的，它们与 `' '`（空格字符串）不是等价的；

OceanBase 数据库支持 3 种空值类型：*SQL 函数中的空值*、*比较条件中的空值*，*条件判断表达式中的空值*；

##### 2.1 SQL 函数中的空值
SQL 函数中的空值指的是 SQL 函数的参数为空值。当 SQL 函数的参数为空值时，大多数标量函数都返回 `NULL`，聚合函数（`COUNT()` 和 `GROUPING()` 除外）会忽略空值；

当使用 `ORDER BY` 进行排序时，对于升序排序，`NULL` 值排在其他值之后；对于降序排序，`NULL` 值排在其他值之前；

| 空值 | 说明 |  
| :------------- | :---------- |
| 标量函数中的空值 | 当出现空值时，您可以使用 `NVL()` 函数返回返回值确定空值 |
| 聚合函数中的空值 | 使用 `AVG`、`MAX`、`SUM` 等聚合函数时，为 `NULL` 的记录会被忽略 |
SQL 函数中的空值指的是 SQL 函数的参数存在空值。当 SQL 函数的参数为空值时，大多数标量函数都返回 NULL，分析函数会忽略空值；


###### 2.1.1 标量函数中的空值
当出现空值时，您可以使用 `NVL()` 函数的返回值确定空值；

```sql
-- NVL() 函数的表达式：NVL(expr1,expr2)
	-- 如果 `expr1` 不是 `NULL`，返回 `expr1`，否则返回 `expr2`；

-- 使用示例：
-- 指定 expr1 参数为 NULL，查询 NVL(expr1，0) 表达式的返回值；
obclient> SELECT NVL(NULL,0) FROM DUAL;
	-- 输出：0；-- expr1 是 NULL，则表达式 NVL(expr1，0) 的返回值为 0；
```
有关 NVL 函数的详细信息，请参见 [NVL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000823256)，；


###### 2.1.2 聚合函数中的空值
在使用 `AVG`、`MAX`、`SUM`、`COUNT` 等聚合函数时，为 `NULL` 的记录会被忽略；

```sql
-- 向表 `tbl_a` 中插入数据并执行查询语句；
CREATE TABLE tbl_a (col_a varchar2(1), col_b int );
INSERT INTO tbl_a VALUES (NULL, 3), (NULL, NULL), (NULL, 1);

obclient> SELECT * FROM tbl_a;
+-------+-------+
| COL_A | COL_B |
+-------+-------+
| NULL  |     3 |
| NULL  |  NULL |
| NULL  |     1 |
+-------+-------+
3 rows in set

-- 使用 AVG、MAX、SUM 等聚合函数的返回结果如下，NULL 的记录被忽略了；
obclient> SELECT AVG(col_b) FROM tbl_a;     -- 输出：2
obclient> SELECT MAX(col_b) FROM tbl_a;     -- 输出：3
obclient> SELECT SUM(col_b) FROM tbl_a;     -- 输出：4
obclient> SELECT COUNT(col_b) FROM tbl_a;   -- 输出：2
obclient> SELECT COUNT(col_a) FROM tbl_a;   -- 输出：0
obclient> SELECT COUNT(*) FROM tbl_a;       -- 输出：3
```


##### 2.2 比较条件中的空值
比较条件中的空值是指与任何其他条件做比较的 `NULL`；

测试空值只能用比较运算符 `IS NULL` 和 `IS NOT NULL`，因为 `NULL` 表示缺少数据，所以 `NULL` 和其它值没有可比性，即不能用等于、不等于、大于或小于和其它数值比较，当然也包括空值本身；

另外，OceanBase 数据库在计算 `DECODE` 函数时认为两个空值是相等的。如果两个空值出现在复合键中，则认为它们也相等。即如果复合键的所有非空字段都相等，OceanBase 数据库才会考虑使用两个包含空值的复合键；

| 条件 | A 值 | 结果 | 备注 |
| :------------- | :---------- |:---------- |:---------- |
| A IS NULL | 10 | FALSE | |
| A IS NOT NULL | 10 | TRUE | |
| A IS NULL	 | NULL | TRUE | |
| A IS NOT NULL | NULL | FALSE | |


##### 2.3 条件判断表达式中的空值
条件判断表达式中的空值指的是条件 `= NULL`、`!= NULL`、 `NULL =`、`NULL ！=` 中的 `NULL`，作逻辑判断使用，判断结果不返回任何行，即 `UNKNOWN`；

在 OceanBase 数据库中，测试空值要使用比较运算符 `IS NULL`，返回结果为 `TRUE` 或 `FALSE`。但是条件判断表达式中空值的判断结果 `UNKNOWN` 与 `FALSE` 不同，`NOT FALSE` 判断结果为 `TRUE`，`NOT UNKNOWN` 判断结果仍然为 `UNKNOWN`；


### 参考文档
1. *NULL 值*：*MySQL 模式*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000822902)，；
2. *空值概述*：*Oracle 模式*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000822729)，；


