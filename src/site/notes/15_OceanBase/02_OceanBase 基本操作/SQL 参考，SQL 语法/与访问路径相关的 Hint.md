---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/SQL 参考，SQL 语法/与访问路径相关的 Hint/","dgPassFrontmatter":true}
---


### 与访问路径相关的 Hint

与访问路径相关的 Hint 包括 `INDEX Hint` 和 `FULL Hint`；

#### 1 INDEX Hint

`INDEX` Hint 指示优化器对指定的表使用索引扫描。您可以将  `INDEX` Hint 用于基于函数、域、B-Tree、位图和位图联接的索引；

```sql
-- INDEX Hint 的语法如下：
	/*+ INDEX ( [ @queryblock ] tablespec [ indexspec [ indexspec ]... ] ) */

-- INDEX Hint 的示例如下：
SELECT /*+ INDEX (employees emp_department_ix)*/ employee_id, department_id
FROM employees
WHERE department_id > 50;
```

> [!NOTE] Hint 的行为取决于  `indexspec`  规范：
>
> - 如果  `INDEX` Hint 指定了一个单个可用索引，则数据库将对该索引执行扫描。优化器不考虑全表扫描或表上另一个索引的扫描；
> - 如果  `INDEX` Hint 指定了可用索引的列表，那么优化器将考虑扫描列表中每个索引的成本，然后以最低的成本执行索引扫描。如果数据库从这个列表中扫描多个索引并合的访问路径成本最低，数据库将选用这种扫描方案。数据库不考虑对没有在列表中的索引进行全表扫描或扫描；
> - 如果  `INDEX` Hint 没有指定具体的索引，那么优化器将考虑表上每个可用索引的扫描成本，然后以最低的成本执行索引扫描。如果数据库扫描多个索引并合的访问路径成本最低，数据库将选用这种扫描方案。优化器不考虑全表扫描；

#### 2 FULL Hint

`FULL` Hint 指示优化器对指定的表执行全表扫描；

```sql
-- FULL Hint 的语法如下：
	/*+ FULL ( [ @queryblock ] tablespec ) */

-- FULL Hint 的示例如下：
SELECT /*+ FULL(e) */ employee_id, last_name
FROM hr.employees e
WHERE last_name LIKE :al;
```

示例中，数据库对表  `employees`  执行一次完整的表扫描来执行这条语句，即使有一个由  `WHERE`  子句中的条件提供的索引在列  `last_name`  上；

在  `FROM`  子句中，表  `employees`  有个别名  `e`，因此 Hint 必须根据表的别名而不是名称来引用该表。即使在  `FROM`  子句中指定了 Schema 名，也不要在 Hint 中引用它们；

#### 3 USE_COLUMN_TABLE

在 OceanBase 数据库 V4.3.0 版本中新增了 `USE_COLUMN_TABLE` Hint，该 Hint 用于指定对表对象使用列存扫描。对于没有启用列存储的表，使用  `USE_COLUMN_TABLE`  无效。

```sql
-- 语法:
	/*+ USE_COLUMN_TABLE ( [ @ qb_name ] tablespec) */
```

使用示例：
```sql
create table t(c1 int, c2 int ,c3 int) with column group (all columns, each column);

explain basic select /*+USE_COLUMN_TABLE(t)*/ * from t;
+------------------------------------------------------------------+
| Query Plan                                                       |
+------------------------------------------------------------------+
| ================================                                 |
| |ID|OPERATOR              |NAME|                                 |
| --------------------------------                                 |
| |0 |COLUMN TABLE FULL SCAN|t   |                                 |
| ================================                                 |
| Outputs & filters:                                               |
| -------------------------------------                            |
|   0 - output([t.c1], [t.c2], [t.c3]), filter(nil), rowset=16     |
|       access([t.c1], [t.c2], [t.c3]), partitions(p0)             |
|       is_index_back=false, is_global_index=false,                |
|       range_key([t.__pk_increment]), range(MIN ; MAX)always true |
+------------------------------------------------------------------+
```

#### 4 NO_USE_COLUMN_TABLE

在 OceanBase 数据库 V4.3.0 版本中新增了 `NO_USE_COLUMN_TABLE` Hint，该 Hint 指定禁止对表对象使用列存扫描；

```sql
-- 语法:
	/*+ NO_USE_COLUMN_TABLE ( [ @ qb_name ] tablespec ) */
```

使用示例：
```sql
create table t(c1 int, c2 int ,c3 int) with column group (all columns, each column);

explain basic select /*+NO_USE_COLUMN_TABLE(t)*/ * from t;
+------------------------------------------------------------------+
| Query Plan                                                       |
+------------------------------------------------------------------+
| =========================                                        |
| |ID|OPERATOR       |NAME|                                        |
| -------------------------                                        |
| |0 |TABLE FULL SCAN|t   |                                        |
| =========================                                        |
| Outputs & filters:                                               |
| -------------------------------------                            |
|   0 - output([t.c1], [t.c2], [t.c3]), filter(nil), rowset=16     |
|       access([t.c1], [t.c2], [t.c3]), partitions(p0)             |
|       is_index_back=false, is_global_index=false,                |
|       range_key([t.__pk_increment]), range(MIN ; MAX)always true |
+------------------------------------------------------------------+
```

### 参考文档

1. _与访问路径相关的 Hint_：[V4.2.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000512396)，[V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000644531)，；
