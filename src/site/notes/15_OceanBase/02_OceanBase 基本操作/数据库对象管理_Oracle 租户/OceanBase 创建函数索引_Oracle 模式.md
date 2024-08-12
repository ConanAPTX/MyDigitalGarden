---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/OceanBase 创建函数索引_Oracle 模式/","dgPassFrontmatter":true}
---



### OceanBase 创建函数索引_Oracle 模式

基于表中一列或多列的值进行计算后的结果建立的索引称为`函数索引`。函数索引是一种优化技术，使用函数索引可以在查询时快速定位匹配的函数值，从而避免重复计算，提高查询效率；

#### 1 创建索引注意事项

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/oracle/oracle/#55df93" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



> [!NOTE] `创建索引的限制`：
> 1. 在 OceanBase 数据库中，索引名称必须在表的范围内保证唯一；
> 2. 索引名称的长度不能超过 128 字节；
> 3. 唯一索引可以有多个，但其列值必须唯一；
> 4. 如果要使用局部唯一索引去对数据唯一性做约束，那么局部唯一索引中必须包含表分区键；

</div></div>


##### 1.1 函数索引使用限制

在 OceanBase 数据库的 Oracle 模式中，对函数索引的表达式进行了限制，禁止部分系统函数的表达式作为函数索引，具体的函数列表请参见 [函数索引支持的系统函数列表](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220156) 和 [函数索引不支持的系统函数列表](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220159)，；


#### 2 创建函数索引
基于表中一列或多列的值进行计算后的结果建立的索引称为`函数索引`。函数索引是一种优化技术，使用函数索引可以在查询时快速定位匹配的函数值，从而避免重复计算，提高查询效率；

您可以使用 `CREATE INDEX` 语句来创建函数索引。函数索引也可以是唯一索引；
```sql
-- 语法如下：
	CREATE [UNIQUE] INDEX index_name ON table_name (expr);
/* 参数介绍：
	`[UNIQUE]`：可选，表示唯一索引。创建唯一索引时需要添加对应的关键字。
	`index_name`：待创建的函数索引的名称。
	`table_name`：指定待创建索引的表的表名。
    `expr`：合法的函数索引表达式，不能是布尔表达式，例如 `c1=c1` 不支持。
*/
```


使用以下 SQL 语句创建一个名为 `test_tbl5` 的表，并为表 `test_tbl5` 创建一个基于 `id` 列的函数索引；

```sql
-- 1.创建表 `test_tbl5`
CREATE TABLE test_tbl5(id NUMBER, name VARCHAR2(18));

-- 2.在表 `test_tbl5` 上基于 `id` 列创建一个的名为 `idx_test_tbl5_id` 的索引
CREATE INDEX dx_test_tbl5_id ON test_tbl5 ((id+1));

-- 3.使用下面 SQL 语句可以查看创建的函数索引
SELECT INDEX_NAME,INDEX_TYPE,TABLE_OWNER,TABLE_NAME,UNIQUENESS FROM user_indexes WHERE table_name='TEST_TBL5';
```

### 参考文档

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/my-sql/ocean-base/#830d2c" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



2. `创建和管理索引` 

</div></div>

