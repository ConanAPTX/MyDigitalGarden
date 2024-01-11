---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/数据库对象管理_MySql 租户/创建和管理序列_MySql 租户/","dgPassFrontmatter":true}
---


### 创建和管理序列_MySql 租户

在 OceanBase 数据库中，序列（Sequence）是数据库按照一定规则生成的自增数字序列号，通常是一组间隔的数值；
由于序列具有自增的特性，故通常会被用作主键和唯一键；

#### 1 查看序列

在 OceanBase 数据库的 MySQL 租户中可以通过 `SHOW SEQUENCES` 语句查看序列；
还可以通过查询 oceanbase.DBA_OB_SEQUENCE_OBJECTS 视图来查看当前租户内的所有序列信息，包括序列 ID、序列名、序列所属的数据库、序列取值的起始值、序列每次取值的步长、序列的循环属性以及序列可取的最大值和最小值等信息；有关该视图字段的更多说明，参见：[oceanbase.DBA_OB_SEQUENCE_OBJECTS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220061)，；

```sql
-- 通过 SHOW SEQUENCES 语句查看序列
SHOW SEQUENCES;								-- 查看当前租户创建的所有序列
SHOW SEQUENCES LIKE '%s%';		            -- 查看序列名中含 s 的所有序列
SHOW SEQUENCES IN test;				        -- 查看数据库 test 中的所有序列
SHOW SEQUENCES WHERE sequence_name='seq1';	-- 查看当前租户内名为 seq1 的序列

-- 通过视图查看序列
SELECT * FROM oceanbase.DBA_OB_SEQUENCE_OBJECTS;
```

#### 2 创建，修改，删除序列

OceanBase 数据库 `V3.2.3 之前的版本`不支持在 `MySQL 租户`下创建序列，更多详细情况：[OceanBase 数据库 MySQL 租户不支持序列](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000207741)，；

##### 2.1 创建序列
OceanBase 数据库中，出于性能考虑，OceanBase 数据库建议您使用默认的 `CACHE with NOORDER` 方式；不建议使用 ORDER with NO CACHE ，这种模式下，每次调用 `NEXTVAL` 都会触发一次内部表 `SELECT` 与 `UPDATE`，会影响数据库的性能；详情请参考：[高并发业务下使用全局有序的序列的性能问题](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000366652)，；

可以使用 CREATE SEQUENCE 语句来创建序列。更多的信息，参见 [CREATE SEQUENCE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221386)，；

```sql
-- 创建序列
CREATE SEQUENCE seq1 START WITH 1 MINVALUE 1 MAXVALUE 10 INCREMENT BY 2 NOCYCLE NOORDER CACHE 30;
	-- 序列seq1：起始值为1，最小值为1，最大值为2，自增步数为2；
/*
	IF NOT EXISTS：可选，如果待创建的序列已存在，并且没有指定 IF NOT EXISTS，则系统会报错；
	database_name：可选，用于指定序列所在的数据库。如果不指定 database_name，则默认为创建当前登录数据库的序列； 
	START WITH：用于指定序列的起始值。该值必须小于等于 MAXVALUE，且大于等于 MINVALUE；
	  	如果不指定，则升序时默认取值为最小值，降序时默认取值为最大值；
	MINVALUE int_value | NOMINVALUE：用于指定序列的最小值，取值范围为[-1027 ~ (1027-1)]；
		如果指定为 NOMINVALUE，则升序时最小值为 1，降序时最小值为 -(1027-1)；
		如果不指定MINVALUE，则缺省为 NOMINVALUE；
	MAXVALUE int_value | NOMAXVALUE：用于指定序列的最大值，取值范围为[(-1027+1) ~ (1028-1)]；
		如果指定为 NOMAXVALUE，则升序时最大值为 (1028-1)，降序时最大值为 -1；
		如果不指定 MAXVALUE，则缺省为 NOMAXVALUE；
	INCREMENT BY：用于指定序列的自增步数。该值不可以为 0。如果指定为正数，则序列为升序；如果指定为负数，则序列为降序。如果不指定，则缺省值为 1；
	CACHE：用于指定在内存中预分配的自增值个数，默认为 20；
	ORDER | NOORDER：用于指定序列的值是否按顺序生成；ORDER 表示按顺序生成，NOORDER 表示不按顺序生成。默认为 NOORDER；
	CYCLE | NOCYCLE：用于指定序列的值是否循环生成；CYCLE 表示循环生成，NOCYCLE 表示不循环生成。默认为 NOCYCLE；
*/
```


> [!NOTE] `注意事项：`
>  1. 如果同时指定 MINVALUE 和 MAXVALUE，则 MINVALUE 必须小于 MAXVALUE；
>  2. MAXVALUE 和 MINVALUE 之差必须大于等于 INCREMENT BY int_value；
>  3. CACHE 的值必须大于 1，如果 CACHE INT_VALUE 的值为 1，则等价于 NOCACHE；
>  4. 指定了 CYCLE，且 INCREMENT BY 的值小于 0 时，必须指定 MINVALUE；
>  5. 指定了 CYCLE 时，CACHE 的数量必须不能超过一个 CYCLE；

##### 2.2 修改序列
序列创建成功后，可以使用 ALTER SEQUENCE 语句来修改序列的属性；OceanBase 数据库支持修改序列的起始值、最小值、最大值、步长和循环属性；
修改序列详情：[`MySql_4.2.1_修改序列`](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220188)，更多信息，请参见 [ALTER SEQUENCE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221403)，；

```sql
ALTER SEQUENCE seq2 MAXVALUE 1024 CYCLE;  -- 修改序列 seq2 的最大值，并指定自增值循环生成

ALTER SEQUENCE seq3 RESTART;  -- 重置序列
ALTER SEQUENCE seq4 RESTART START WITH 3; -- 重置序列，并让其取值从 3 开始

-- 修改序列的起始值时，RESTART 在语句中的位置并不一定要求在序列名后，它可以在语句中的任意位置
ALTER SEQUENCE seq4 START WITH 3 RESTART MINVALUE -100 CYCLE;
	-- CYCLE | NOCYCLE：用于指定序列的值是否循环生成；
  	-- CYCLE 表示循环生成，NOCYCLE 表示不循环生成。默认为 NOCYCLE；
```

##### 2.3 删除序列
可以使用 DROP SEQUENCE 语句来删除序列，更多的信息，请参见 [DROP SEQUENCE](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221433)，；

```sql
/* 删除序列语法：
	DROP SEQUENCE [IF EXISTS] [database_name.]sequence_name;
语句使用说明：
	执行该语句的用户需要具备 DROP 权限；
	如果指定 IF EXISTS，即使待删除的序列不存在，也不会报错；如果不指定，则系统会报错；
	database_name. 用于指定序列所在的数据库。如果不指定 database_name. ，则默认为当前登录数据库的序列；
*/
DROP SEQUENCE S1;		-- 删除序列 S1
```

#### 3 引用（使用）序列
序列提供了两个伪列 `CURRVAL` 和 `NEXTVAL`，用于返回当前的序列值和下一个序列值；

任何时候对 `CURRVAL` 的引用，都将返回当前序列的值，即最后一次对 `NEXTVAL` 引用时返回的值；第一次引用 `NEXTVAL` 将返回序列的初始值，后续对 `NEXTVAL` 的引用将按照上一次序列的返回值加上序列定义的步长后返回一个新值；更多详情：[MySql-4.2.1-引用序列](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220184)，；

序列的 CURRVAL 和 NEXTVAL 的值可以用于以下位置：`顶层 SELECT 语句的选择列表中`；`INSERT 语句中的 VALUE 子句中`；`UPDATE 语句中的 SET 子句中`；

```sql
-- 通过 SEQ_FOO.CURRVAL 获取 SEQ_FOO 序列的当前值
SELECT SEQUENCE_NAME.CURRVAL FROM DUAL;     -- 执行多少次 Sequence 号都不会变化

-- 通过 SEQ_FOO.NEXTVAL 获取 SEQ_FOO 序列的下一个自增值
SELECT SEQUENCE_NAME.NEXTVAL FROM DUAL;     -- 每执行一次 Sequence 号就会增加
```



### 参考文档：
1. `查看序列:`[MySql_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220187)，；




