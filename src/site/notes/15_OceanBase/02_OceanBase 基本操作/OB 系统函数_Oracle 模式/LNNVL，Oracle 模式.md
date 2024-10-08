---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/OB 系统函数_Oracle 模式/LNNVL，Oracle 模式/","dgPassFrontmatter":true}
---


### LNNVL，Oracle 模式
#### 1 适用版本

#### 2 描述
该函数功能是当条件中的一个或者两个操作数可能为空时，提供判断方法。将条件作为参数，如果条件为 `FALSE` 或 `UNKNOWN`，则返回 `TRUE`；如果条件为 `TRUE`，则返回 `FALSE`；
该函数可以在 `WHERE` 子句中使用，也可以作为 `CASE` 表达式中的 `WHEN` 条件；

#### 3 语法
```sql
-- 语法：LNNVL (condition)
-- 参数解释：
	condition 是指判断条件。假设 a = 2，b 值为 NULL，下表显示了 LNNVL 函数的返回值；
-- 返回类型：
	返回布尔型 TRUE 或 FALSE;
```

| 条件 | 条件判断结果 | LNNVL 返回值 |
| :------------- | :----------: | :------------: |
| `a` = `1` | `FALSE` | `TRUE` |
| `a` = `2` | `TRUE` | `FALSE` |
| `a IS NULL` | `FALSE` | `TRUE` |
| `b` = `1` | `UNKNOWN` | `TRUE` |
| `b IS NULL` | `TRUE` | `FALSE` |
| `a` = `b` | `UNKNOWN` | `TRUE` |


#### 4 使用示例
创建表 `tbl1`，插入五条数据。查询列 `col2` 中小于 `50` 并且包括为空的数据；

```sql
obclient> CREATE TABLE tbl1 (col1 INT,col2 INT);
Query OK, 0 rows affected

obclient> INSERT INTO tbl1 VALUES (1,30),(2,null),(3,50),(4,80),(5,10);
Query OK, 5 rows affected
Records: 5  Duplicates: 0  Warnings: 0

obclient> SELECT * FROM tbl1 WHERE LNNVL(col2 >= 50);
+------+------+
| COL1 | COL2 |
+------+------+
|    1 |   30 |
|    2 | NULL |
|    5 |   10 |
+------+------+
3 rows in set
```


### 参考文档
1. *LNNVL*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000823253)，；


