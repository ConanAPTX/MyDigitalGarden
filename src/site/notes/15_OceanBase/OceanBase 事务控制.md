---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/OceanBase 事务控制/","dgPassFrontmatter":true}
---


### OceanBase 事务控制
#### 1 MySQL 事务控制  
##### 1.1 开启，提交，回滚事务  

```sql  
-- 开启事务  
BEGIN;  
START TRANSACTION;  
-- 除了以上方法之外，关闭事务自动提交之后再执行的第一条语句；  
SET autocommit = 0; -- 设置事务不自动提交；  
  
COMMIT; -- 提交事务  
  
-- 回滚事务  
-- 语法：ROLLBACK [WORK] [AND [NO] CHAIN] [[NO] RELEASE]  
ROLLBACK; -- 回滚事务  
```  

##### 1.2 自动，隐式提交  
在 MySql 租户中，其事务是默认自动提交的，该特性是由 autocommit 变量控制的，值为 1，即自动提交；值为 0，即不自动提交；  

```sql  
-- 语法：SET autocommit = {0 | 1}  
-- autocommit：用于设置是否自动提交事务；值为 1，表示自动提交；值为 0，表示不自动提交；  
  
SET autocommit = 1; -- 值为 1，默认值，表示自动提交；  
SET autocommit = 0; -- 值为 0，表示不自动提交；  
```  

隐式提交是用户未发出 COMMIT/ROLLBACK 等结束事务的语句给 OceanBase 数据库，而 OceanBase 数据库自动将当前活跃的事务执行 COMMIT 提交的过程；

> [!NOTE] 隐式提交发生在以下情形：  
> 1. 执行一个开启事务的语句；  
> 2. 执行 DDL；  


##### 1.3 自动回滚  
自动回滚是用户未发出 ROLLBACK 指令，而是 OceanBase 数据库内部发起的回滚，通常发生在以下情形：  
  
1. session 断开；  
2. 事务执行超时（ob_trx_timeout）；  
	1. ob_trx_timeout 用于设置事务超时时间，单位为微秒；  
3. 活跃事务的 session 超过一定时长没有语句执行（ob_trx_idle_timeout）；  
	1. ob_trx_idle_timeout 用于设置事务空闲超时时间，即事务中两条语句之间的执行间隔超过该值时超时，单位为微秒；  


#### 2 Oracle 事务控制  
##### 2.1 开启事务  
OceanBase 数据库在 Oracle 模式下开启事务共有以下两种方式：**自动开启**；**手动开启**；  
自动开启：在无事务开启的 Session 端执行 DML 语句（除了 SELECT）、DCL 语句或 DDL 语句时，会默认开启一个事务；  
手动开启：除了兼容 Oracle 数据库的使用方式外，OceanBase 数据库还支持手动开启事务；

```sql  
-- 开启事务  
START TRANSACTION; -- 开启事务  
START TRANSACTION READ ONLY; -- 开启只读事务  
BEGIN;  
  
COMMIT; -- 提交事务  
  
-- 回滚事务，语法：ROLLBACK [WORK];  
ROLLBACK; -- 回滚事务  
```  

##### 2.2 只读事务  
###### 2.2.1 只读不开事务  

为了针对只读语句进行优化，在 Session 无事务开启的情况下，执行 SELECT 语句即可进入只读不开事务路径。只读不开事务跳过事务上下文等流程，只读取所需的必要信息，将数据返回用户；

```sql  
SELECT * FROM t1; -- 只读不开事务  
SELECT * FROM t2; -- 只读不开事务  
INSERT INTO t1 VALUES(1); -- 开启事务  
```  

###### 2.2.2 只读事务  

只读事务不能存在对数据的修改，也因此相对读写事务可进行优化，只读事务不创建事务上下文，故在回滚时也无需执行任何清理操作；

```sql  
obclient> START TRANSACTION READ ONLY; -- 开启只读事务  
```  

#### 3 语句超时与事务超时  
  
1. `ob_query_timeout`：用于设置 SQL 最大执行时间，单位是微秒；【推荐使用默认值】  
	1. 【*GLOBAL，SESSION*】【int】；默认值为 10000000，即 10s，取值范围为: [0, 3216672000000000]；  
	2. 若语句执行时间超过此值会给应用返回语句超时的错误，错误码为 -6212，并回滚语句；  
2. `ob_trx_timeout`：用于设置事务超时（未提交）时间，单位为微秒；【推荐使用默认值】  
	1. 【*GLOBAL，SESSION*】【int】；默认值为 100000000，即 100s，取值范围为: [NULL]； 
	2. 从 V4.0.0 版本开始默认值由 100000000 调整为 86400000000，即 86400s，1天；  
	3. 事务执行时间超过此值会给应用返回事务超时的错误，错误码为 -6210，此时需要应用发起 ROLLBACK 语句回滚该事务；  
3. `ob_trx_idle_timeout`：用于设置事务空闲超时时间，即事务中两条语句之间的执行间隔超过该值时超时，单位为微秒；  
	1. 【*GLOBAL，SESSION*】【int】；默认值为 120000000，即120s，取值范围为: [100000000，+∞]；  
	2. 从 V4.0.0 版本开始默认值由 120000000 调整为 86400000000，即 86400s，1天；  
	3. 表示 Session上一个事务处于的 IDLE 状态的最长时间，即长时间没有 DML 语句或结束该事务，则超过该时间值后，事务会自动回滚，再执行 DML 语句会给应用返回错误码 -6224，应用需要发起 ROLLBACK 语句清理 Session 状态；  
	4. 根据需要设置，一般保持默认值；  

```sql  
SHOW VARIABLES LIKE 'ob_query_timeout';  
SHOW VARIABLES LIKE 'ob_trx_timeout';  
SHOW VARIABLES LIKE 'ob_trx_idle_timeout';  
  
SET SESSION ob_query_timeout = 10000000000;  
SET SESSION ob_trx_timeout = 100000000000;  
SET SESSION ob_trx_idle_timeout = 120000000000;  
/*  
ob_query_timeout：用于设置 SQL 最大执行时间，单位为微秒；  
ob_trx_timeout：用于设置事务超时（未提交）时间，单位为微秒；  
ob_trx_idle_timeout：用于设置事务空闲超时时间，即事务中两条语句之间的执行间隔超过该值时超时，单位为微秒；  
*/  
```  


#### 4 事务查询  
虚拟表 \_\_all_virtual_trans_stat 可用于查询系统中当前所有的活跃事务。有关活跃事务的详细信息请参见 [活跃事务](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356643)]，；  

```sql  
-- 查询系统中当前所有的活跃事务  
SELECT trans_id FROM oceanbase.__all_virtual_trans_stat;  
/*  
state 字段标识了事务所处的状态:  
	0，表示事务处于活跃状态，所有修改对其他事务不可见；  
	1，表示事务已经开始提交，目前处于 PREPARE 状态，读取该事务的修改可能会被卡住(取决于版本号)；  
	2，表示事务已经开始提交，且目前处于 COMMIT状态，其他事务可以看到该事务的修改(取决于版本号)；  
	3，表示事务已经回滚，处于 ABORT 状态，其他事务不能看到该事务的修改；  
	4，表示事务已经提交或回滚结束，处于 CLEAR 状态；  
	101，表示单分区事务提交完成，处于 COMMIT 状态，其他事务可以看到该事务的修改；  
	102，表示单分区事务已经回滚，其他事务看不到该事务的修改；  
*/  
```  


#### 5 Savepoint  
*Savepoint* 是 OceanBase 数据库提供的可以由用户定义的一个事务内的执行标记。用户可以通过在事务内定义若干标记并在需要时将事务恢复到指定标记时的状态；[Savepoint](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356644)，；  

例如，当用户在执行过程中在定义了某个 Savepoint 之后执行了一些错误的操作，用户不需要回滚整个事务再重新执行，而是可以通过执行 ROLLBACK TO 命令来将 Savepoint 之后的修改回滚；  

```sql  
BEGIN; -- 开启事务  
	INSERT INTO a VALUE(1); -- 插入行 1  
	SAVEPOINT sp1;          -- 创建名为 sp1 的 Savepoint  
	INSERT INTO a VALUE(2); -- 插入行 2  
	SAVEPOINT sp2;          -- 创建名为 sp2 的 Savepoint  
	ROLLBACK TO sp1;        -- 将修改回滚到 sp1  
	INSERT INTO a VALUE(3); -- 插入行 3  
COMMIT; -- 提交事务  
```


### 参考文档



