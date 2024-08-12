---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/创建，删除索引_Oracle 租户/","dgPassFrontmatter":true}
---


### 创建，删除索引_Oracle 租户
索引也叫二级索引，是一种可选的表结构；OceanBase 数据库采用的是聚集索引表模型，对于用户指定的主键，系统会自动生成主键索引，而对于用户创建的其他索引，则是二级索引；您可以根据自身业务需要来决定在哪些字段上创建索引，以便加快在这些字段上的查询速度；


#### 1 删除索引  
```sql
-- 删除索引 
-- 语法：DROP INDEX index_name;

DROP INDEX t3_ind2;
Query OK, 0 rows affected (0.02 sec)
```

#### 2 创建索引
##### 2.1 创建索引语法
创建索引语法：[CREATE INDEX_V4.2.3](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000753257)，；

##### 2.2 创建索引的限制及建议
> [!NOTE] `创建索引的限制`：
> 1. 在 OceanBase 数据库中，索引名称必须在表的范围内保证唯一；
> 2. 索引名称的长度不能超过 128 字节；
> 3. 唯一索引可以有多个，但其列值必须唯一；
> 4. 如果要使用局部唯一索引去对数据唯一性做约束，那么局部唯一索引中必须包含表分区键；
{ #55df93}



> [!NOTE] `索引创建建议`：
> 1. 建议使用能够简洁地描述索引所涵盖的列和用途的名称，例如，`idx_customer_name`。更多命名信息，请参见 [对象命名规范综述](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000640140)；
> 2. 如果全局索引的分区规则和主表的分区规则相同并且分区数相同，建议创建一个局部索引；
> 3. 建议并行下发创建索引的 SQL 语句条数，不要超过租户 Unit 规格中的核数上限。例如，租户的 Unit 规格为 4 核（4C），则建议并发创建索引不超过 4 条；
> 4. 对经常更新的表要避免对其进行过多的索引，对经常用于查询的字段应该创建索引；
> 5. 数据量小的表最好不要使用索引，因为由于数据较少，可能查询全部数据花费的时间比遍历索引的时间还要短，索引就可能不会产生优化效果；
> 6. 当修改性能远远大于检索性能时，不建议创建索引；
> 7. 创建高效索引：
> 	1. 索引要全部包含所需查询的列，包含的列越全越好，这样可以尽可能地减少回表的行数；
> 	2. 等值条件永远放在最前面；
> 	3. 过滤与排序数据量大的放前面；


#### 3 使用命令行创建索引，创建索引  
##### 3.1 创建唯一索引，非唯一索引
如果需要索引列上不存在重复的值，可以创建唯一（`UNIQUE`）索引；

```sql
-- 1.创建表 `test_tbl1`
CREATE TABLE test_tbl1(col1 NUMBER, col2 NUMBER, col3 VARCHAR2(50), PRIMARY KEY(col1));

-- 2.创建唯一索引，非唯一索引
	-- 创建非唯一索引
	CREATE INDEX idx_test_tbl1_col2 ON test_tbl2(col2);
	-- 创建唯一索引
	CREATE UNIQUE INDEX idx_test_tbl1_col2_unique ON test_tbl1(col2);

-- 3.查看表 `test_tbl1` 索引信息
SELECT INDEX_NAME,INDEX_TYPE,TABLE_OWNER,TABLE_NAME,UNIQUENESS FROM user_indexes WHERE table_name='TEST_TBL1';
```

##### 3.2 创建局部索引，全局索引
[[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/OceanBase 创建局部索引，全局索引\|OceanBase 创建局部索引，全局索引]]，；

##### 3.3 创建函数索引
[[15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/OceanBase 创建函数索引_Oracle 模式\|OceanBase 创建函数索引_Oracle 模式]]，；

##### 3.4 创建列存索引

### 参考文档
1. [创建索引](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000640018)，[创建索引](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000641847)，；
2. [观察 OceanBase 数据库创建索引进度](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000564143?back=kb)，；
3. [CREATE INDEX_V4.2.3](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000753257)，创建索引语法详细介绍；
4. *删除索引*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220155)，；


