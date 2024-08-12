---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/03_OceanBase 高阶进阶/事务管理/OceanBase 事务隔离级别/","dgPassFrontmatter":true}
---


### OceanBase 事务隔离级别

#### 1 事务隔离级别概述  
隔离级别是根据事务并发执行过程中必须防止的现象来定义的；可防止的现象包括：*脏读*，*不可重复读*，*幻读*；
关于其定义可以参考：[[11_MySQL/03_高级进阶/MySql 中的事务/脏读，不可重复读，幻读介绍以及实现\|脏读，不可重复读，幻读介绍以及实现]]，；

基于上述3种现象，ANSI 和 ISO/IEC 定义了四种隔离级别，这四种隔离级别如下：`读未提交(Read Uncommitted)`，`读已提交(Read Committed)`，`可重复读(Repeatable Read)`，`可串行化(Serializable)`；
  
*Read Committed*，读已提交：一个事务执行的查询，只能看到这次查询开始之前提交的数据。读已提交无法防止不可重复读和幻读两种异常现象。如果冲突的事务比较少，简单高效的读已提交隔离级别，对应用来说是足够的;  
  
*Repeatable Read*，可重复读：事务内不同时间读到的同一批数据是一致的。无法防止幻读这种异常现象;  
  
*Serializable*，可串行化：一个事务的查询，只能看到事务开始之前提交的数据。这是最严格的隔离级别，可以防止脏读、不可重复读和幻读三种异常现象，事务看起来就像是串行执行的 ；

  | 隔离级别   | 脏读 |   不可重复读 | 幻读 |
| ------------- | :---------- | ------------ | ------------ |
| `读未提交(Read Uncommitted)` | 可能 | 可能 | 可能 |
| `读已提交(Read Committed)` | 不会 | 可能 | 可能 |
| `可重复读(Repeatable Read)` | 不会 | 不会 | 可能 |
| `可串行化(Serializable)` | 不会 | 不会 | 不会 |

#### 2 Oracle 模式的隔离级别  
##### 2.1 Oracle 模式支持的隔离级别  
OceanBase 数据库在 Oracle 模式下，支持以下隔离级别：*读已提交(Read Committed)*，*可串行化(Serializable)*；其默认的隔离级别为【`读已提交(Read Committed)`】；  OceanBase 数据库的 Oracle 模式在这两种隔离级别下的行为与 Oracle 数据库的行为一致；

*读已提交(Read Committed)*：一个事务执行的查询，只能看到这次查询开始之前提交的数据。读已提交无法防止不可重复读和幻读两种异常情况；
*可串行化(Serializable)*：一个事务的查询，只能看到事务开始之前提交的数据。这是最严格的隔离级别，可以防止脏读、不可重复读和幻读三种异常情况，事务看起来就像是串行执行的；

注意事项：在 v4.1.0 版本的官方文档中，Oracle 模式还支持 *可重复读(Repeatable Read)*，有可能是该版本官方文档记录错误；在 V4.3.1 版本中仅支持 2种；

##### 2.2 隔离级别行为对比
Oracle 数据库支持 *读已提交* 和 *可串行化* 隔离级别，OceanBase 数据库的 Oracle 模式在这两种隔离级别下的行为与 Oracle 数据库的行为一致；

##### 2.3 查询及设置事务隔离级别_Oracle 模式  
设置隔离级别有两种方式，分别为事务级别（Transaction level）和会话级别（Session level）；  

> [!NOTE] `隔离级别使用限制`：  
> 1. 不能在事务执行过程中设置隔离级别，否则会报如下错误：  ERROR:ORA-01453: SET TRANSACTION must be first statement of transaction  
> 2. 在开启可串行化隔离级别时需要确保全局时钟服务（Global Timestamp Service）是打开的；  
> 3. Session 需要维护 session 级别的事务隔离级别，在开启事务时获取 session 级别的事务隔离级别，该隔离级别可以被事务级别的隔离级别覆盖；  
  
```sql
-- 1.查询当前事务隔离级别  
SHOW SESSION VARIABLES LIKE 'transaction_isolation';  
SHOW GLOBAL VARIABLES LIKE 'transaction_isolation';  

-- 2.设置事务隔离级别
-- Transaction level，语法：SET TRANSACTION ISOLATION LEVEL [READ COMMITTED | SERIALIZABLE];
obclient> SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;       -- 可串行化(Serializable)  
	-- 事务级别的隔离级别设置后，不会改变配置，仅表示设置了下个事务的隔离级别并开启了事务；  
  
-- Session level，语法：ALTER SESSION SET ISOLATION_LEVEL = [READ COMMITTED | SERIALIZABLE];
obclient> ALTER SESSION SET ISOLATION_LEVEL = SERIALIZABLE;         -- 可串行化(Serializable)  
```

  
#### 3 MySQL 模式的隔离级别  
##### 3.1 MySQL 模式支持的隔离级别  
OceanBase 数据库在 MySQL 模式下，支持三种隔离级别：*读已提交(Read Committed)*，*可重复读(Repeatable Read)*，*可串行化(Serializable)*；OceanBase 数据库默认的隔离级别为 【`读已提交(Read Committed)`】；

*读已提交(Read Committed)*：一个事务执行的查询，只能看到这次查询开始之前提交的数据。读已提交无法防止不可重复读和幻读两种异常情况；
*可重复读(Repeatable Read)*：事务内不同时间读到的同一批数据是一致的；
*可串行化(Serializable)*：该隔离级别类似 Oracle 数据库的 Serializable，并非严格意义上的 Serializable；

MySQL 数据库支持*读未提交*、*读已提交*、*可重复读* 和 *可串行化* 四种隔离级别，OceanBase 数据库的 MySQL 模式在这四种隔离级别上与 MySQL 数据库均有一定差异，详细情况请查看官方文档；


##### 3.2 隔离级别行为对比
MySQL 数据库支持*读未提交*、*读已提交*、*可重复读* 和*可串行化* 四种隔离级别，OceanBase 数据库的 MySQL 模式在这四种隔离级别上与 MySQL 数据库均有一定差异；

`读未提交`： MySQL 数据库支持*读未提交*，而 OceanBase 数据库不支持*读未提交*；
`读已提交`： 在 *读已提交* 隔离级别下判断行是否满足更新条件时，MySQL 数据库使用半一致性读，如果一行已经被并发事务更新，MySQL 数据库会等待并发事务的结束，然后在最新版本上判断是否需要更新；而在 OceanBase 数据库中，无论并发事务是否更新，总是基于语句快照中的版本来判断一行是否满足更新条件；
`可重复读`： 在 *可重复读* 隔离级别下发生写冲突时，MySQL 数据库中后写的事务会等待先写的事务结束，如果先写的事务回滚，后写的事务则在原版本上直接更新，如果先写的事务提交，*后写的事务会在新提交的版本上再进行更新*；OceanBase 数据库中后写的事务会等待先写的事务结束，如果先写的事务回滚，后写的事务则在原版本上直接更新，如果先写的事务提交，*后写的事务会回滚并返回错误*；
`可串行化`： MySQL 数据库的*可串行化* 隔离级别使用两阶段锁（2PL），能够保证严格的可串行化；OceanBase 数据库的*可串行化* 隔离级别使用快照隔离（Snapshot Isolation），不能保证严格的可串行化。
  
##### 3.3 查询及设置事务隔离级别_MySQL 模式  

> [!NOTE] 隔离级别使用限制：
> 1. 不允许在事务执行过程中设置隔离级别，否则会报如下错误：
> 	ERROR 1568 (25001): Transaction characteristics can't be changed while a transaction is in progress
> 2. Global 级别的隔离级别可以被 Session 级别的隔离级别覆盖；


```sql
-- 1.查询当前事务隔离级别  
SHOW SESSION VARIABLES LIKE 'transaction_isolation';  
SHOW GLOBAL VARIABLES LIKE 'transaction_isolation';  

-- 2.设置事务事务隔离界别
	-- 语法：SET [GLOBAL | SESSION] TRANSACTION ISOLATION LEVEL [READ COMMITTED | REPEATABLE READ | SERIALIZABLE];

-- 设置事务事务隔离界别(session 级别)  
SET SESSION TRANSACTION ISOLATION LEVEL Read Committed;   -- 读已提交(Read Committed)，默认事务隔离级别  
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;  -- 可重复读(Repeatable Read)  
SET SESSION TRANSACTION ISOLATION LEVEL Serializable;     -- 可串行化(Serializable)  
  
-- 设置事务事务隔离界别(全局)  
SET GLOBAL TRANSACTION ISOLATION LEVEL Read Committed;    -- 读已提交(Read Committed)，默认事务隔离级别  
SET GLOBAL TRANSACTION ISOLATION LEVEL REPEATABLE READ;   -- 可重复读(Repeatable Read)  
SET GLOBAL TRANSACTION ISOLATION LEVEL Serializable;      -- 可串行化(Serializable)  
```


### 参考文档：  
1. *事务隔离级别概述*：[V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356650)，[v4.1.0](https://www.oceanbase.com/docs/common-oceanbase-database-10000000001701156)，[v4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220991)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000821590)，；








