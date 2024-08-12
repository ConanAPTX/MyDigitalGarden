---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/SQL 参考，SQL 语法/OceanBase 伪列概述/","dgPassFrontmatter":true}
---


### OceanBase 伪列概述

伪列（Pseudocolumn）的行为与表中的列相同，但并未存储具体数值。因此，伪列只具备读属性，您不可以对伪列进行插入、更新、删除的等行为。本章节主要介绍 OceanBase 数据库当前版本所支持的伪列及使用说明；

OceanBase 数据库当前版本支持如下伪列类型：*层次查询伪列*，*序列伪列*，*ORA_ROWSCN 伪列*，*ROWNUM 伪列*，*ROWID 伪列*；

#### 1 层次查询伪列

#### 2 序列伪列

#### 3 ORA_ROWSCN 伪列

#### 4 ROWNUM 伪列
ROWNUM 伪列会对查询结果中的每一行进行编号，其值为该行在查询结果集中的具体位置。例如，第一行返回值 1，第二行返回值 2，之后以此类推。本文主要介绍 ROWNUM 伪列的使用说明；

`ROWNUM` 可以限制返回的行数。如下例所示，返回 `employees` 表中的 5 条数据：
```sql
SELECT * FROM employees WHERE rownum <=5;
SELECT * FROM employees WHERE rownum <=5 ORDER BY age DESC;
	-- 先 rownum，再排序；
SELECT * FROM (SELECT * FROM employees ORDER BY age DESC) WHERE rownum <= 5;
	-- 先排序，再 rownum；
SELECT * FROM employees WHERE rownum > 1;  -- 该 SQL 语句将不返回任何信息；
	-- 如果在 WHERE 子句中指定 ROWNUM 大于任何一个正整数时，总会返回 FALSE；
```



可以通过 `UPDATE` 语句将 `ROWNUM` 数值赋值给表中的某一列，例如：
```sql
UPDATE employees SET id = rownum;
```

此语句将对表 `employees` 的 `id` 列进行 `ROWNUM` 赋值，即依次对 `id` 列赋值 1、2... 直至该表总行数；

> [!NOTE] `注意`：
> 在查询中使用 `ROWNUM` 可能会影响视图优化；

#### 5 ROWID 伪列


### 参考文档
1. *层次查询伪列*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000821921)，；
2. *序列伪列*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000821916)，；
3. *ORA_ROWSCN 伪列*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000821920)，；
4. *ROWNUM 伪列*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000821918)，；
5. *ROWID 伪列*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000821919)，；


