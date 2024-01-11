---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/03_OceanBase 高阶进阶/OceanBase 事务隔离级别/","dgPassFrontmatter":true}
---


### OceanBase 事务隔离级别

#### 1 事务隔离级别概述  
隔离级别是根据事务并发执行过程中必须防止的现象来定义的；可防止的现象包括：脏读，不可重复读，幻读；
关于其定义可以参考：[[11_MySQL/03_高级进阶/MySql 中的事务/脏读，不可重复读，幻读介绍以及实现\|脏读，不可重复读，幻读介绍以及实现]]，；


基于上述3种现象，ANSI 和 ISO/IEC 定义了四种隔离级别，这四种隔离级别如下：`读未提交(Read Uncommitted)`，`读已提交(Read Committed)`，`可重复读(Repeatable Read)`，`可串行化(Serializable)`；
  

  | 隔离级别   | 脏读 |   不可重复读 | 幻读 |
| ------------- | :---------- | ------------ | ------------ |
| `读未提交(Read Uncommitted)` | 可能 | 可能 | 可能 |
| `读已提交(Read Committed)` | 不会 | 可能 | 可能 |
| `可重复读(Repeatable Read)` | 不会 | 不会 | 可能 |
| `可串行化(Serializable)` | 不会 | 不会 | 不会 |




#### 2 Oracle 模式的隔离级别  
##### 2.1 Oracle 模式支持的隔离级别  
OceanBase 数据库在 Oracle 模式下，支持以下隔离级别，其默认的隔离级别为【读已提交(Read Committed)】：  
  
`Read Committed` ：读已提交； 
一个事务执行的查询，只能看到这次查询开始之前提交的数据。读已提交无法防止不可重复读和幻读两种异常现象。如果冲突的事务比较少，简单高效的读已提交隔离级别，对应用来说是足够的;  
  
`Repeatable Read`：可重复读；
事务内不同时间读到的同一批数据是一致的。无法防止幻读这种异常现象;  
  
`Serializable`：可串行化；
一个事务的查询，只能看到事务开始之前提交的数据。这是最严格的隔离级别，可以防止脏读、不可重复读和幻读三种异常现象，事务看起来就像是串行执行的 ；
  
  
##### 2.2 查询及设置事务隔离级别_Oracle 模式  
设置隔离级别有两种方式，分别为事务级别及 session 级别；  

> [!NOTE] 使用限制：  
> 1. 不能在事务执行过程中设置隔离级别，否则会报如下错误：  ERROR:ORA-01453: SET TRANSACTION must be first statement of transaction  
> 2. 在开启可串行化隔离级别时需要确保全局时钟服务（Global Timestamp Service）是打开的；  
> 3. Session 需要维护 session 级别的事务隔离级别，在开启事务时获取 session 级别的事务隔离级别，该隔离级别可以被事务级别的隔离级别覆盖；  
  
```sql
-- 查询当前事务隔离级别  
SHOW SESSION VARIABLES LIKE 'transaction_isolation';  
SHOW GLOBAL VARIABLES LIKE 'transaction_isolation';  
  
-- Transaction level  
obclient> SET TRANSACTION ISOLATION LEVEL SERIALIZABLE; -- 可串行化(Serializable)  
-- 事务级别的隔离级别设置后，不会改变配置，仅表示设置了下个事务的隔离级别并开启了事务；  
  
-- Session level  
obclient> ALTER SESSION SET ISOLATION_LEVEL = SERIALIZABLE; -- 可串行化(Serializable)  
```

  
  
#### 3 MySQL 模式的隔离级别  
##### 3.1 MySQL 模式支持的隔离级别  
OceanBase 数据库在 MySQL 模式下，支持三种隔离级别，其默认的隔离级别为【读已提交(Read Committed)】：  
`Read Committed`：读已提交；
一个事务执行的查询，只能看到这次查询开始之前提交的数据。读已提交无法防止不可重复读和幻读两种异常现象。如果冲突的事务比较少，简单高效的读已提交隔离级别对应用来说是足够的；  
  
`Repeatable Read`：可重复读；
事务内不同时间读到的同一批数据是一致的。无法防止幻读这种异常现象；  
  
`Serializable`：可串行化；
该隔离级别类似 Oracle 数据库的 Serializable，并非严格意义上的 Serializable  
  
  
##### 3.2 查询及设置事务隔离级别_MySQL 模式  

```sql
-- 语法：SET [GLOBAL | SESSION] TRANSACTION ISOLATION LEVEL REPEATABLE READ;  
  
-- 查询当前事务隔离级别  
SHOW SESSION VARIABLES LIKE 'transaction_isolation';  
SHOW GLOBAL VARIABLES LIKE 'transaction_isolation';  
  
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
1. [事务隔离级别概述](https://www.oceanbase.com/docs/common-oceanbase-database-10000000001701156)，；








