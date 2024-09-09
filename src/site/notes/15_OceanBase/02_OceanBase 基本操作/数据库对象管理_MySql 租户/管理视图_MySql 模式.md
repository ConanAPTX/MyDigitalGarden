---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/管理视图_MySql 模式/","dgPassFrontmatter":true}
---



### 管理视图_MySql 模式


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/oracle/oracle/#1" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



#### 1 注意事项
创建，修改视图除了需要 【创建视图】，【修改视图】的权限，还需要以下权限：

> [!NOTE] `注意事项`：
> 创建视图时，需要访问视图中涉及对象的【直接权限】，详细情况：[[15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/OceanBase 管理用户和权限/用户和权限概述#4.4 间接权限\|用户和权限概述#4.4 间接权限]]，；



</div></div>



#### 1 创建普通视图

```sql
/*
MySQL 模式下，创建视图的 SQL 语法如下：
	create_view_stmt:
	  CREATE [OR REPLACE] VIEW view_name [(column_name_list)] AS select_stmt;
	  [WITH [CASCADED | LOCAL] CHECK OPTION];
	column_name_list:
	    column_name [, column_name ...]

字段说明：
	`OR REPLACE`：表示如果要创建的视图名称已存在，则使用新的定义重新创建视图。
    `select_stmt`：定义视图的 `SELECT` 语句，该语句中的信息可以从基表或其他视图中选取；
    `column_name_list`：与基表一样，视图必须具有唯一的列名，不能重复。缺省情况下，由 `SELECT` 语句检索的列名将用作视图列名。
	    如果想为视图列定义明确的名称，可使用可选的 `column_name_list` 子句，ID 之间使用逗号分隔。`column_name_list` 中的名称数目必须等于 `SELECT`语句检索的列数。
	    `SELECT` 语句检索的列可以是对表列的简单引用。也可以是使用函数、常量值、运算符等的表达式。
    
	`WITH CHECK OPTION` 子句用于防止系统对 `select_statement` 中 `WHERE` 子句为真的行以外的行进行插入或更新操作；
		在使用 `WITH CHECK OPTION` 子句创建视图时，可以通过添加 `CASCADED` 或 `LOCAL` 关键字来明确检查的范围。
		    如果使用 `LOCAL` 关键字，则检查视图的 `WHERE`子句，然后将检查递归到基础视图并应用相同的规则。
		    如果使用 `CASCADED` 关键字，则检查视图的 `WHERE` 子句，然后将检查递归到底层视图，向它们添加 `WITH CASCADED CHECK OPTION`（底层视图的定义保持不变），并应用相同的规则。
		    如果没有在 `WITH CHECK OPTION` 中指定 `CASCADED` 或 `LOCAL` 关键字，则默认使用 `CASCADED` 关键字；
	        如果不使用 `WITH CHECK OPTION` 子句，则在创建视图时，系统不检查视图的 `WHERE` 子句，然后将检查递归到底层视图，并应用相同的规则；
*/
```


```sql
CREATE  VIEW stock_item AS
     SELECT /*+ leading(s) use_merge(i) */ i_price, i_name, i_data, s_i_id, s_w_id
     FROM stok s, item i
     WHERE s.s_i_id = i.i_id;
Query OK, 0 rows affected
```



#### 2 修改普通视图
可以使用 `CREATE OR REPLACE VIEW` 语句来对普通视图进行修改；

```sql
show create view stock_item;

-- 修改视图 `stock_item`;
CREATE OR REPLACE VIEW stock_item AS
     SELECT /*+ leading(s) use_merge(i) */ i_price, i_name, i_data, s_i_id, s_w_id
     FROM stok s, item i
     WHERE s.s_i_id = i.i_id;
```


#### 3 删除视图
可以使用 `DROP VIEW` 语句删除一个或多个视图，删除视图并不会删除视图所引用的表；若当前视图被其他视图所引用，则视图删除后将会导致依赖当前视图的其他视图的查询失败；删除视图时，需确保当前用户具备该视图的 `DROP` 权限；

```sql
/*
-- MySQL 模式下，删除视图的 SQL 语法如下：
	drop_view_stmt:
	    DROP VIEW [IF EXISTS] view_name_list;
	
	view_name_list:
	    view_name [, view_name_list]
字段说明：
	使用 `IF EXISTS` 关键字可以防止由于视图不存在而出错；
    如果 `view_name_list` 中包含一部分存在和一部分不存在的视图，执行可能报错但是存在的视图依然会被删除；
*/

-- 示例
DROP VIEW V1;           -- 删除视图 `V1`
-- DROP VIEW V1, V2;    -- 删除视图 `V1`,V2；【不推荐使用】
```


### 参考文档
1. *管理视图*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218616)，；



