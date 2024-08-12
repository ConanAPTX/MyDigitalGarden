---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/OceanBase 索引概述/","dgPassFrontmatter":true}
---



### OceanBase 索引概述
#### 1 关于索引
表创建成功后，可以在表的一个或多个列上创建索引以加速表上的 SQL 语句执行速度。索引使用正确的话，可以减少物理 IO 或者逻辑 IO。OceanBase 数据库的表存储模型为索引聚集表模型，如果用户未指定主键，系统会自动生成一个隐藏主键；

> [!NOTE] `索引的优缺点`：
> 1. *索引的优点*：
> 	1. 加快数据的查询速度；
> 	 1. 通过使用索引，可以在查询的过程中，使用优化隐藏器，提高系统的性能；
>  2. *索引的缺点*：
> 	 1. 创建索引和维护索引要耗费时间，并且随着数据量的增加所耗费的时间也会增加；
> 	 2. 当对表中的数据进行增加、删除、修改时，索引也需要动态的维护，降低了数据的维护速度；

> [!NOTE] `索引的使用原则`：
> 1. 对经常更新的表要避免对其进行过多的索引，对经常用于查询的字段应该创建索引；
> 2. 数据量小的表最好不要使用索引，因为由于数据较少，可能查询全部数据花费的时间比遍历索引的时间还要短，索引就可能不会产生优化效果；
> 3. 当修改性能远远大于检索性能时，不应该创建索引；

#### 2 索引选择概述
SQL 优化是指调优这个 SQL 的执行计划。

> [!NOTE] `计划调优通常面临两类问题`：
> 1. 计划空间太小，优化器可以选择的执行计划都不够好，实践中看到的是 SQL 始终跑不快；
> 2. 计划空间太大，优化器可能会误选一个性能较差的执行计划，实践中看到的是 SQL 会抖动，时快时慢，极端情况下会影响整个集群，打爆 CPU；

当计划空间太小的时候，常见的优化方式是增加合适的索引，这可以增加基表路径的选择，扩大计划空间。只要充分理解索引选择和优化，这一点是比较容易做到的。另一方面，当计划空间太大的时候，优化器选择合适计划的难度会变高，常见的调优方式是增加 Hint 来控制优化器的计划选择；

事实上，在 OceanBase 数据库中，我们也可以通过规划设计更好的索引来帮助优化器按照规则裁剪掉很多不好的执行计划，避免抖动的发生；


#### 3 创建高效索引

1. `减少读行`，；[减少读行](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052888)，；
	1. 
2. `避免回表`，；[避免回表](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052889)，；
	1. 索引要全部包含所查询的列：包含的列越全越好，这样可以尽可能的减少回表的行数；
		1. 一般来说，需要创建覆盖索引的场景需要满足几个特征：
			1. `1`. 数据库的 I/O 压力非常大；`2`. 大量的 I/O 是若干高频查询回查主表产生的随机 I/O 导致的；`3`. 高频查询需要读取的列并不多；
3. `避免排序`，；[避免排序](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052890)，；

##### 3.1 其他
4. 索引表与普通数据表一样都是实体表，在数据表进行更新的时候会先更新索引表然后再更新数据表；
6. 等值条件永远放在最前面；
7. 过滤与排序数据量大的放前面；
8. 选择具有高选择性、频繁在 where 从句中出现、频繁在 join 关联字段中的字段；
9. 不对函数或表达式中的字段建索引，要么就建函数索引；
10. 创建一个索引时，评估该索引给查询带来的性能优化是否比因其而引起 INSERT，UPDATE，DELETE 操作的性能下降；
11. 以及索引占用的空间更要值得；
12. 在常被修改到字段上建索引需要进行评估；


#### 4 123

| `索引中的字段` | `索引中的字段` | `命中索引的SQL` | `未命中索引的SQL` | 备注 |
| :-------------: | :-------------: | :---------- | :------------ | :------------ |
| 等值查询 | (A,B,C) | where A = ? and B = ? and C = ?<br>where A = ? and B = ?<br>where A = ? and C = ? | where B = ? and C = ?<br>where C = ? | |
| 范围查询 | (A,B,C) | where A > ? and B > ? and C < ?<br>where A > ? and B > ?<br>where A > ? and C < ? | where B > ? and C < ?<br>where C in (?,?) | |
| 等值和范围查询 | (A,B,C) | where A = ? and B = ? and C > ?<br>where A = ? and B > ? and C = ?<br>where A = ? and B > ? and C > ? | where B > ? and C < ?<br>where C in (?,?)<br>where C = ? | |

1. 等值查询：
	1. *条件的先后顺序不影响索引能效*，如 where A = ? and B = ? 和 where B = ? and A = ? 效果相同；
	2. 从索引能效来看: 【Where A =? And B=? and C=?】>【Where A=? and B=? 】> 【Where A=? and C=?】
2. 范围查询：
	1. 常见的范围查询有： 大于、小于、大于等于、小于等于、between…and 、 in(?,?)
	2. *遇到第一个范围查询字段后，后续的字段不参与索引过滤*（不走索引）；
	3. 如【where A > ? and B > ? and C < ?】、【where A > ? and B > ?】 、【where A > ? and C < ?】只能走A字段的索引
3. 等值和范围查询：
	1. *遇到第一个范围查询字段后，后续的字段不参与索引过滤*；（不走索引）；
	2. 从索引能效看：
		1. 【where A = ? and B = ? and C > ?】>【where A = ? and B > ? and C > ?】
		2. 【where A = ? and B > ? and C = ?】=【where A = ? and B > ? and C > ?】



### 参考文档
1. *索引选择概述*：[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052887)，； 
2. `创建和管理索引`
{ #830d2c}

	1. _V4.2.1：参考指南 > 数据库对象管理 > MySql 模式 > 创建和管理索引_
		1. *创建和管理索引*：包含：关于索引，创建索引，查看索引，删除索引，函数索引支持的系统函数列表，函数索引不支持的系统函数列表；
			1. *关于索引*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220158)，；
			2. *创建索引*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220208)，；
			3. _查看索引_：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220206)，；
	2. _V4.2.1：参考指南 > 数据库对象管理 > Oracle 模式 > 创建和管理索引_
		1. *创建和管理索引*：包含：关于索引，创建索引，查看索引，删除索引，函数索引支持的系统函数列表，函数索引不支持的系统函数列表；
			1. *创建索引*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220157)，；
			2. _查看索引_：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220160)，；
			4. *函数索引支持的系统函数列表*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220156)，；
			5. *函数索引不支持的系统函数列表*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220159)，；
3. `在分区表上建立索引`
{ #25392c}

	1. *V4.2.1：参考指南 > 数据库对象管理 > Oracle 模式 > 创建和管理分区  > 在分区表上建立索引*
		1. *在分区表上建立索引*：包含：索引概述，局部索引，全局索引；
			1. *索引概述*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221070)，；
	2. *V4.2.1：参考指南 > 数据库对象管理 > MySql 模式 > 创建和管理分区  > 在分区表上建立索引*
		1. *在分区表上建立索引*：包含：索引概述，局部索引，全局索引；
			1. *索引概述*：[V4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221076)，；

