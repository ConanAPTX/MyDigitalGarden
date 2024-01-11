---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_Oracle 租户/管理序列_Oracle 租户/","dgPassFrontmatter":true}
---

### 管理序列_Oracle 租户
在 OceanBase 数据库中，序列（Sequence）是 Oracle 租户的数据库对象，可以产生不重复的值，在表需要不重复的列做主键时很有用；

#### 1 查看序列
序列创建成功后，可以通过 USER_SEQUENCES、ALL_SEQUENCES 和 DBA_SEQUENCES 视图来查看自己创建的序列；更多详细信息请参见：[DBA_SEQUENCES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219218)，[ALL_SEQUENCES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219304)，[USER_SEQUENCES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000219312)，；

```sql
SELECT * FROM USER_SEQUENCES; 	-- 查询当前用户拥有的所有 SEQUENCE
SELECT * FROM ALL_SEQUENCES;	-- 查询用户可访问的所有 SEQUENCE
SELECT * FROM DBA_SEQUENCES;	-- 查询当前租户下的所有序列
```

#### 2 创建，使用序列

序列只可用于顶层 `SELECT ITEM`、`INSERT` 值与 `UPDATE` 赋值；序列不可用于子查询、Filter、group by 等语句，更多详细情况：[OceanBase 数据库 Oracle 模式中序列介绍](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000000013)，；

> [!NOTE] `使用序列的注意事项：`
> 1. OceanBase 数据库 Oracle 租户中，序列能够保证连续递增吗？
> 	1. 单机内保证递增，多机之间不保证递增；
> 	2. 单机和多机都不保证连续。如果发生了宕机或执行了回滚，则值可能出现跳跃。因此若需要严格等差递增序列时，不建议您使用数据库中的序列；

##### 2.1 创建序列

OceanBase 数据库中，出于性能考虑，OceanBase 数据库建议您使用默认的 `CACHE with NOORDER` 方式；不建议使用 ORDER with NO CACHE ，这种模式下，每次调用 `NEXTVAL` 都会触发一次内部表 `SELECT` 与 `UPDATE`，会影响数据库的性能；详情请参考：[高并发业务下使用全局有序的序列的性能问题](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000366652)，；

可以使用 CREATE SEQUENCE 语句来创建序列，更多信息请参见：[CREATE SEQUENCE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222096)，；

```sql
-- 创建序列
CREATE SEQUENCE test.seq_1 START WITH 1 MINVALUE 1 MAXVALUE 10 INCREMENT BY 2 NOCYCLE NOORDER CACHE 30;
	-- 序列 seq_1：起始值为1，最小值为1，最大值为10，自增步数为2；
/*
	START WITH int_value：指定序列的起始值；int_value 必须小于等于 MAXVALUE，且大于等于 MINVALUE；若不指定，则升序时默认取值为最小值，降序时默认取值为最大值；
	MINVALUE int_value：指定序列的最小值，int_value 的取值范围是 [-9223372036854775808，9223372036854775807]；
		NOMINVALUE：NOMINVALUE 为默认值。 如果指定为 NOMINVALUE，则升序时最小值为 1，降序时最小值为 -9223372036854775808；
	MAXVALUE int_value：指定序列的最大值，int_value 的取值范围是 [-9223372036854775808，9223372036854775807]；
		NOMAXVALUE：NOMAXVALUE 为默认值。 如果指定为 NOMAXVALUE，则升序时最大值为9223372036854775807，降序时最大值为 -1；
	INCREMENT BY int_value：指定序列的自增步数。int_value 不可以为 0；
		若指定为正数，则序列为升序；如果指定为负数，则序列为降序。 不指定时缺省值为 1；
	CYCLE | NOCYCLE：用于指定序列值是否循环；若序列值循环，则需要指定最大值或最小值；
		CYCLE：指定序列的值循环生成，即序列在达到其最大值或最小值后继续生成值；在升序达到最大值后，它会生成最小值。在降序序列达到其最小值后，它会生成其最大值；
		NOCYCLE：该值为默认值，指定序列在达到其最大值或最小值后无法生成更多值；
	ORDER | NOORDER：
		NOORDER 为默认值，即不保证序列的值按顺序生成，但是保证唯一；
		ORDER 指定序列的值按顺序生成；
	CACHE int_value | NOCACHE：
		CACHE int_value：指定在内存中预分配的自增值个数，int_value 默认为 20。CACHE int_value 的值必须大于 1，如果 CACHE INT_VALUE 的值为 1，则等价于 NOCACHE；
		NOCACHE	指定序列的值为未预先分配。 如果同时省略 CACHE 和 NOCACHE，则数据库默认缓存 20 个序列号；
*/
```

##### 2.2 使用序列
```sql
-- 创建表
CREATE TABLE t1(id number NOT NULL PRIMARY KEY, name varchar(50) , gmt_create date NOT NULL DEFAULT SYSDATE);

-- 创建序列
CREATE SEQUENCE seq_1 START WITH 10 MINVALUE 1 INCREMENT BY 1 NOCYCLE NOORDER CACHE 50; 
	-- 序列 seq_1：起始值为10，最小值为1，自增步数为1；

SELECT seq_1.CURRVAL FROM DUAL;		-- 查询序列的当前值

-- 查询序列的下一个值
SELECT seq_1.NEXTVAL FROM DUAL; 	-- 第1次返回值为 1；
SELECT seq_1.NEXTVAL FROM DUAL;		-- 第1次返回值为 3；

INSERT INTO t1(id,name) VALUES(seq_1.NEXTVAL, 'A');
INSERT INTO t1(id,name) VALUES(seq_1.NEXTVAL, 'B');
INSERT INTO t1(id,name) VALUES(seq_1.NEXTVAL, 'C');
UPDATE t1 SET id = seq_1.NEXTVAL WHERE name = 'B';

SELECT * FROM t1;
```

#### 3 修改序列
OceanBase 数据库支持修改序列的起始值、最小值、最大值、步长和循环属性；
序列创建成功后，可以使用 ALTER SEQUENCE 语句来修改序列的属性，更多的信息，请参见 [ALTER SEQUENCE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222085)，；

```sql
-- 修改序列 seq2 的最大值，并指定自增值循环生成
ALTER SEQUENCE seq2 MAXVALUE 1024 CYCLE; 

-- 修改序列的起始值
ALTER SEQUENCE seq3 RESTART;		-- 重置序列
	-- 如果 INCREMENT BY 的值大于 0，则可以重置序列后，让其取值从 MINVALUE 开始；
	-- 如果 INCREMENT BY 的值小于 0，则可以重置序列后，让其取值从 MAXVALUE 开始；
ALTER SEQUENCE seq4 RESTART START WITH 3;  -- 重置序列，并让其取值从 3 开始
ALTER SEQUENCE seq4 START WITH 3 RESTART MINVALUE -100 CYCLE;
	-- 修改序列的起始值时，RESTART 在语句中的位置并不一定要求在序列名后，它可以在语句中的任意位置；
```

#### 4 删除序列
以使用 DROP SEQUENCE 语句来删除序列，更多的信息，请参见：[DROP SEQUENCE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000222100)，；

```sql
-- 删除序列，语法：DROP SEQUENCE sequence_name;
DROP SEQUENCE S1; 	-- 删除序列 S1
```

### 参考文档



