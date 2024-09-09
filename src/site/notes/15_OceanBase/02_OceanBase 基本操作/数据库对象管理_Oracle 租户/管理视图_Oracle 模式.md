---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理视图_Oracle 模式/","dgPassFrontmatter":true}
---



### 管理视图_Oracle 模式
视图用来展示表的查询结果。大部分能使用表的地方，都可以使用视图；如果经常访问的数据分布在多个表里时，使用视图是最好的方法；

#### 1 注意事项
创建，修改视图除了需要 【创建视图】，【修改视图】的权限，还需要以下权限：

> [!NOTE] `注意事项`：
> 创建视图时，需要访问视图中涉及对象的【直接权限】，详细情况：[[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/OceanBase 管理用户和权限/用户和权限概述#4.4 间接权限\|用户和权限概述#4.4 间接权限]]，；


#### 2 创建普通视图
可以使用 `CREATE VIEW` 语句来创建普通视图；创建后，可以对该视图执行 DML 语句；

```sql
-- Oracle 模式下，创建视图的 SQL 语法如下：
	create_view_stmt:
	  CREATE [OR REPLACE] [[NO] FORCE] VIEW view_name [(column_name_list)] AS select_stmt;
	
	column_name_list:
	    column_name [, column_name ...]
/*
参数：
	OR REPLACE：表示如果要创建的视图名称已存在，则使用新的定义重新创建视图；
	select_stmt：定义视图的 `SELECT` 语句，该语句中的信息可以从基表或其他视图中选取；
	[NO] FORCE：如果创建视图时，不希望考虑该视图是否存在基表或存在引用的对象类型，以及视图所在 Schema 的所有者是否具有权限时，则需要指定 `FORCE`；
	    如果已确认基表存在且视图所在 Schema 的所有者具有权限，则可以指定 `NO FORCE`。默认为 `NO FORCE`；
	column_name_list：与基表一样，视图必须具有唯一的列名，不能重复。缺省情况下，由 `SELECT` 语句检索的列名将用作视图列名；
	    如果想为视图列定义明确的名称，可使用可选的 `column_name_list` 子句，ID 之间使用逗号分隔。`column_name_list` 中的名称数目必须等于 `SELECT`语句检索的列数；
    SELECT 语句检索的列可以是对表列的简单引用。也可以是使用函数、常量值、运算符等的表达式；
*/
```


```sql
-- 使用示例：选择表 `tbl1` 中的 `col1`、`col2` 列来创建视图 `view1`
CREATE OR REPLACE FORCE VIEW view1(vcol1, vcol2) AS SELECT col1, col2 FROM tbl1;
```

#### 3 修改普通视图
您可以使用 `CREATE OR REPLACE VIEW` 语句来对普通视图进行修改；

```sql
-- 修改视图 stock_item
CREATE OR REPLACE VIEW stock_item AS
     SELECT /*+ leading(s) use_merge(i) */ i_price, i_name, i_data, s_i_id, s_w_id, s_order_cnt, s_ytd, s_remote_cnt, 
	     s_quantity, s_data, s_dist_01, s_dist_02, s_dist_03, s_dist_04, s_dist_05, s_dist_06, s_dist_07, s_dist_08, s_dist_09, s_dist_10
     FROM stok s, item i
     WHERE s.s_i_id = i.i_id;
Query OK, 0 rows affected (0.02 sec)
```


#### 4 删除视图
可以使用 `DROP VIEW` 语句删除一个或多个视图，删除视图并不会删除视图所引用的表；如果当前视图被其他视图所引用，则被依赖视图删除后将会导致依赖当前视图的其他视图的查询失败；

删除视图时，需确保当前用户具备该视图的 `DROP` 权限；

```sql
-- Oracle 模式下，删除视图的 SQL 语法如下：
	-- DROP VIEW view_name;

obclient> DROP VIEW v1;    -- 删除视图 v1；
```


### 3 参考文档
1. *管理视图_Oracle 模式*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218615)，；


