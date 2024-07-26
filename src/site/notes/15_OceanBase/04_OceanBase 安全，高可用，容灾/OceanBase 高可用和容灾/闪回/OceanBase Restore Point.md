---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/闪回/OceanBase Restore Point/","dgPassFrontmatter":true}
---


### OceanBase Restore Point

OceanBase 数据库在 V2.2.7x 版本中提供了 `Restore Point` 功能，允许用户在租户上创建 `Restore Point`，将历史版本的数据保存下来；`Restore Point` 功能类似于租户的快照点，您可以通过闪回查询的方式来访问特定版本的历史数据；

> [!NOTE] `使用注意事项`：
> 1. 不支持物理备份；
> 2. 不支持主备库；
> 3. 不支持在 sys 租户下创建 Restore Point；
> 4. 每个租户内最多可创建 10 个 Restore Point ；
> 5. 创建 Restore Point 后，如果对创建 Restore Point 前就存在的表执行 DDL 语句，系统会报错；

#### 1 前提条件
由于 Restore Point 功能依赖 GTS 维护全局的一致性快照，故在使用 Restore Point 时，需要开启 GTS；

```sql
-- 开启 GTS 阿
obclient> set GLOBAL ob_timestamp_service='GTS';
```


#### 2 创建或删除 Restore Poin
保留的 Restore Point 对应的数据会占用相应的存储资源，使用完后需要及时手动删除 Restore Point 的操作；

```sql
-- 创建 Restore Point
obclient> CREATE RESTORE POINT restore_point;

-- 删除 Restore Point
obclient> DROP RESTORE POINT restore_point;
```

#### 3 查询 Restore Poin 信息
```sql
-- 查询 Restore Point
-- 1. 租户管理员登录数据库

-- 2. 
obclient> SELECT * FROM oceanbase.V$RESTORE_POINT;  -- MySQL 模式
obclient> SELECT * FROM V$RESTORE_POINT;            -- Oracle 模式
/*
	TENANT_ID 列为创建 Restore Point 的租户的 ID。
	SNAPSHOT 列为 Restore Point 对应的数据版本号。
	TIME 列为 Restore Point 的创建时间。
	NAME 列为 Restore Point 的名称
*/
```


#### 4 使用 Restore Poin
根据查询到的版本号，执行以下语句，进行数据的查询分析；

##### 4.1 MySQL 模式
```sql
-- MySQL 模式
obclient> SELECT * FROM table_name AS OF SNAPSHOT 1638501121443992;

-- 使用示例

```


##### 4.2 Oracle 模式
```sql
-- Oracle 模式
obclient> SELECT * FROM table_name AS OF SCN 1638501121443992;

-- 使用示例
	-- 创建一个测试表 test 并插入数据
	CREATE TABLE test ( ID NUMBER PRIMARY KEY, NAME varchar2(20));
	INSERT INTO test VALUES (1,'LI');
	commit;
	
	-- 创建一个 Restore Point
	obclient> CREATE RESTORE POINT restore_point;
	
	-- 向表中再插入一些数据并提交
	obclient> INSERT INTO test VALUES (2, 'WANG');
	obclient> INSERT INTO test VALUES (3, 'SU');
	obclient> commit;
	
	--
	SELECT * FROM test;  	-- 查询表 test 当前版本的数据
	
	-- 查看创建的 Restore Point 信息
	obclient> SELECT * FROM oceanbase.V$RESTORE_POINT;  -- MySQL 模式
	bclient> SELECT * FROM V$RESTORE_POINT;  --Oracle 模式
	
	-- 根据查询到的版本号查看该版本之前的数据
	SELECT * FROM test AS OF SCN 1637549041740744;
```


### 参考文档


