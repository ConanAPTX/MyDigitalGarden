---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 安全权限/Oracle 模式下的数据透明加密/","dgPassFrontmatter":true}
---


### Oracle 模式下的数据透明加密
#### 1 为新创建的表设置透明加密
##### 1.1 配置 internal 方式的透明加密
以 internal 方式的存储主密钥，其加密信息主要在内部表中管理；为了避免日志回放时的循环依赖，该模式中的 clog 不做加密；

1.  开启 internal 方式的透明加密；
    配置项 `tde_method` 用于设置透明表空间加密的方式，默认为 `none`，表示关闭透明表空间加密；更多配置项 `tde_method` 的说明信息，参见 [tde_method](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000820863)，；

> [!NOTE] `注意`：
> 配置项 `tde_method` 一旦设置，就不能再修改；

```sql
-- 1.管理员用户登录到集群的 Oracle 租户；

-- 2.开启 internal 方式的透明加密；
obclient> ALTER SYSTEM SET tde_method='internal';

-- 3.执行以下语句，确认该租户上所有 OBServer 节点上的配置项 `tde_method` 的值均为 `internal`；
obclient> SHOW PARAMETERS LIKE 'tde_method';
```

4.  创建，开启 Keystore；
```sql
-- 4.创建 Keystore；
	-- 语法：ADMINISTER KEY MANAGEMENT CREATE KEYSTORE keystore_name IDENTIFIED BY password;
	
	-- 使用示例：
	obclient> ADMINISTER KEY MANAGEMENT CREATE KEYSTORE sectest1 IDENTIFIED BY **3**;

-- 5.开启 Keystore
	-- 语法：ADMINISTER KEY MANAGEMENT SET KEYSTORE OPEN IDENTIFIED BY password;
		-- 其中，`password` 为上一步设置的 Keystore 的密钥

	-- 使用示例：
	obclient> ADMINISTER KEY MANAGEMENT SET KEYSTORE OPEN IDENTIFIED BY **3**;
```

6.  执行以下语句，生成主密钥；
```sql
-- 6.生成主密钥
	-- 语法：ADMINISTER KEY MANAGEMENT SET KEY IDENTIFIED BY password;
		-- 语句中的 `password` 即为之前设置的 Keystore 的密钥
	
	-- 使用示例：
	 obclient> ADMINISTER KEY MANAGEMENT SET KEY IDENTIFIED BY **3**;
```

7.  创建表空间并指定加密算法;
    可以指定加密算法 `aes-256`、`aes-128`、`aes-192`、`sm4-cbc`、`aes-128-gcm`、`aes-192-gcm`、`aes-256-gcm`、`sm4-gcm`；
```sql
-- 创建表空间并指定加密算法；
obclient> CREATE TABLESPACE sectest_ts1 ENCRYPTION USING 'aes-256';
```


##### 1.2 在加密的表空间中创建新表
```sql
-- 1.普通用户登录到 Oracle 租户；

-- 2.创建表并指定表空间；
obclient> CREATE TABLE t1 (id1 int, id2 int) TABLESPACE sectest_ts1;
	-- 创建后，该表对应的任何事务都需要加密

-- 3.确认表空间内的表是否标记为加密；
	-- 执行以下语句，查看 `encryptionalg` 是否为之前设置的 `aes-256`；
    obclient> SELECT table_name,encryptionalg,encrypted FROM V$OB_ENCRYPTED_TABLES;
    +------------+---------------+-----------+
    | TABLE_NAME | ENCRYPTIONALG | ENCRYPTED |
    +------------+---------------+-----------+
    | T1         | aes-256       | YES       |
    +------------+---------------+-----------+
    1 row in set
	    -- `encryptionalg` 为 `aes-256`，且 `encrypted` 为 `YES` 则表示表加密配置成功；
```
更多视图 `V$OB_ENCRYPTED_TABLES` 的字段及说明信息，参见 [V$OB_ENCRYPTED_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819503)，；


#### 2 为已有表设置透明加密
##### 2.1 配置 internal 方式的透明加密

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/04-ocean-base/ocean-base/oracle/#1-1-internal" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



##### 1.1 配置 internal 方式的透明加密
以 internal 方式的存储主密钥，其加密信息主要在内部表中管理；为了避免日志回放时的循环依赖，该模式中的 clog 不做加密；

1.  开启 internal 方式的透明加密；
    配置项 `tde_method` 用于设置透明表空间加密的方式，默认为 `none`，表示关闭透明表空间加密；更多配置项 `tde_method` 的说明信息，参见 [tde_method](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000820863)，；

> [!NOTE] `注意`：
> 配置项 `tde_method` 一旦设置，就不能再修改；

```sql
-- 1.管理员用户登录到集群的 Oracle 租户；

-- 2.开启 internal 方式的透明加密；
obclient> ALTER SYSTEM SET tde_method='internal';

-- 3.执行以下语句，确认该租户上所有 OBServer 节点上的配置项 `tde_method` 的值均为 `internal`；
obclient> SHOW PARAMETERS LIKE 'tde_method';
```

4.  创建，开启 Keystore；
```sql
-- 4.创建 Keystore；
	-- 语法：ADMINISTER KEY MANAGEMENT CREATE KEYSTORE keystore_name IDENTIFIED BY password;
	
	-- 使用示例：
	obclient> ADMINISTER KEY MANAGEMENT CREATE KEYSTORE sectest1 IDENTIFIED BY **3**;

-- 5.开启 Keystore
	-- 语法：ADMINISTER KEY MANAGEMENT SET KEYSTORE OPEN IDENTIFIED BY password;
		-- 其中，`password` 为上一步设置的 Keystore 的密钥

	-- 使用示例：
	obclient> ADMINISTER KEY MANAGEMENT SET KEYSTORE OPEN IDENTIFIED BY **3**;
```

6.  执行以下语句，生成主密钥；
```sql
-- 6.生成主密钥
	-- 语法：ADMINISTER KEY MANAGEMENT SET KEY IDENTIFIED BY password;
		-- 语句中的 `password` 即为之前设置的 Keystore 的密钥
	
	-- 使用示例：
	 obclient> ADMINISTER KEY MANAGEMENT SET KEY IDENTIFIED BY **3**;
```

7.  创建表空间并指定加密算法;
    可以指定加密算法 `aes-256`、`aes-128`、`aes-192`、`sm4-cbc`、`aes-128-gcm`、`aes-192-gcm`、`aes-256-gcm`、`sm4-gcm`；
```sql
-- 创建表空间并指定加密算法；
obclient> CREATE TABLESPACE sectest_ts1 ENCRYPTION USING 'aes-256';
```



</div></div>



##### 2.2 将已有表移入加密表空间
```sql
-- 1.普通用户登录到数据库的 Oracle 租户；

-- 2.将表 `t1` 移入表空间 `sectest_ts1` 中；
obclient> ALTER TABLE t1 TABLESPACE sectest_ts1;

-- 3.修改索引表，将表上的索引加入到表空间 `sectest_ts1` 中；
obclient> ALTER INDEX idx1 TABLESPACE sectest_ts1;
```


##### 2.3 对表进行透明加密
1. *普通用户登录到数据库的 Oracle 租户*;
```sql
-- 1.普通用户登录到数据库的 Oracle 租户;
```

2. *设置表的 `progressive_merge_num` 值，给表做全量合并或者渐进合并*；

`progressive_merge_num` 用于设置表的渐进合并的轮次，默认为 0，表示进行增量合并。如果值设置为 1，则表示进行全量合并；在给表做合并操作时，通常使用全量合并方式，如果表中的数据量很大，开启全量合并可能会导致单次合并时间过长，则建议使用渐进合并；

```sql
-- 2.1 对表执行全量合并
	-- 1. 将 progressive_merge_num 的值设置为 1；
	obclient> ALTER TABLE t1 set progressive_merge_num = 1;
	
	-- 2.手动发起一轮合并；
		-- 说明：对表执行全量合并后，待全量合并完成，所有表数据均已加密完成，您可以直接通过查看 V$OB_ENCRYPTED_TABLES 视图来确认加密完成情况；

	-- 3.待合并完成后，再将 progressive_merge_num 的值设置为 0；
	obclient> ALTER TABLE t1 set progressive_merge_num = 0;
```
手动发起合并的操作：
[[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/OceanBase 合并管理概述\|OceanBase 合并管理概述]]，[[15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/合并的触发方式\|合并的触发方式]]， [手动触发合并](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819291)，；

```sql
 -- 2.2 对表执行渐进合并
	-- 1.将 progressive_merge_num 的值设置为大于 1 的数，并执行 SHRINK SPACE 操作，准备做渐进合并；
	-- 示例如下：
	obclient> ALTER TABLE t1 set progressive_merge_num = 3;
	obclient> ALTER TABLE t1 SHRINK SPACE;   

	-- 2.手动发起多轮渐进合并，使表和其上的索引的所有存量宏块完成加密。
	obclient> ALTER SYSTEM MAJOR FREEZE;   -- 发起一轮渐进合并的语句如下： 
		-- 说明：渐进合并过程中，您可以通过查询 V$OB_ENCRYPTED_TABLES 视图来实时关注加密的完成情况；
```


3. *完成后，您可以查看以下视图，确认所有宏块是否已完成加密*；
```sql
-- 3.完成后，您可以查看以下视图，确认所有宏块是否已完成加密；
    obclient> SELECT * FROM V$OB_ENCRYPTED_TABLES;
    +----------+------------+---------------+---------------+-----------+----------------------------------+-------------+------------------+------------------+--------+--------+
    | TABLE_ID | TABLE_NAME | TABLESPACE_ID | ENCRYPTIONALG | ENCRYPTED | ENCRYPTEDKEY                     | MASTERKEYID | BLOCKS_ENCRYPTED | BLOCKS_DECRYPTED | STATUS | CON_ID |
    +----------+------------+---------------+---------------+-----------+----------------------------------+-------------+------------------+------------------+--------+--------+
    |   500005 | T1         |        500004 | aes-256       | YES       | xxxxxxxxxxxxxxxxxxxxxxxxxxxx66F3 |      xxxx03 |                0 |                0 | NORMAL |   1010 |
    +----------+------------+---------------+---------------+-----------+----------------------------------+-------------+------------------+------------------+--------+--------+
    1 row in set
	-- 根据查询结果，如果 `BLOCKS_DECRYPTED` 字段的值为 `0`，则表示所有宏块都已完成加密；
```
更多视图 `V$OB_ENCRYPTED_TABLES` 的字段及说明信息，参见 [V$OB_ENCRYPTED_TABLES](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819503)，；


#### 3 解除加密
将表从加密的表空间中移出即可解除表的加密。

##### 3.1 背景信息
假设表 `t1` 的加密状态如下，且表 `t1` 在加密的表空间 `sectest_ts1` 中。

```shell
obclient> SELECT * FROM V$OB_ENCRYPTED_TABLES;
+----------+------------+---------------+---------------+-----------+----------------------------------+-------------+------------------+------------------+--------+--------+
| TABLE_ID | TABLE_NAME | TABLESPACE_ID | ENCRYPTIONALG | ENCRYPTED | ENCRYPTEDKEY                     | MASTERKEYID | BLOCKS_ENCRYPTED | BLOCKS_DECRYPTED | STATUS | CON_ID |
+----------+------------+---------------+---------------+-----------+----------------------------------+-------------+------------------+------------------+--------+--------+
|   500005 | T1         |        500004 | aes-256       | YES       | xxxxxxxxxxxxxxxxxxxxxxxxxxxx66F3 |      xxxx03 |                0 |                0 | NORMAL |   1010 |
+----------+------------+---------------+---------------+-----------+----------------------------------+-------------+------------------+------------------+--------+--------+
1 row in set

obclient> SHOW CREATE TABLE t1;
+-------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| TABLE | CREATE TABLE
     |
+-------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| T1    | CREATE TABLE "T1" (
  "ID1" NUMBER(38),
  "ID2" NUMBER(38)
) COMPRESS FOR ARCHIVE REPLICA_NUM = 1 BLOCK_SIZE = 16384 USE_BLOOM_FILTER = FALSE TABLET_SIZE = 134217728 PCTFREE = 0  TABLESPACE "SECTEST_TS1" |
+-------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set
```

##### 3.2 将表从加密表空间中移出
1.  管理员登录到集群的 Oracle 租户；
2.  对表执行渐进合并；
```sql
-- 1.将 `progressive_merge_num` 的值设置为大于 `1` 的数，并执行 `SHRINK SPACE` 操作，准备做渐进合并；
	obclient> ALTER TABLE t1 SET progressive_merge_num = 3;       
	obclient> ALTER TABLE t1 SHRINK SPACE;

-- 2.手动发起多轮渐进合并;
	 obclient> ALTER SYSTEM MAJOR FREEZE;   -- 发起一轮渐进合并的语句如下
```

> [!NOTE] 说明：
> 解除加密是异步过程，解密过程需要对表进行渐进合并保证所有数据解密；

3.  创建未加密的表空间 `ts2`；


```sql
-- 3.创建未加密的表空间 ts2；
	obclient> CREATE TABLESPACE ts2;

-- 4.将表 `t1` 从加密的表空间 `sectest_ts1` 中移到未加密的表空间 `ts2` 中。
	obclient> ALTER TABLE t1 TABLESPACE ts2;

	-- 操作成功后，执行 `SHOW CREATE TABLE` 语句，可以看到表 `t1` 已经移到了表空间 `ts2` 中；
    obclient> SHOW CREATE TABLE t1;
    +-------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | TABLE | CREATE TABLE                                                                                                                                                                                        |
    +-------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | T1    | CREATE TABLE "T1" (
      "ID1" NUMBER(38),
      "ID2" NUMBER(38)
    ) COMPRESS FOR ARCHIVE REPLICA_NUM = 1 BLOCK_SIZE = 16384 USE_BLOOM_FILTER = FALSE TABLET_SIZE = 134217728 PCTFREE = 0  TABLESPACE "TS2" |
    +-------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    1 row in set
```

5.  查询 `V$OB_ENCRYPTED_TABLES` 视图，确认表的加密状态；
```sql
obclient>  SELECT * FROM V$OB_ENCRYPTED_TABLES;
+------------------+------------+------------------+---------------+-----------+--------------+-------------+------------------+------------------+------------+--------+
| TABLE_ID         | TABLE_NAME | TABLESPACE_ID    | ENCRYPTIONALG | ENCRYPTED | ENCRYPTEDKEY | MASTERKEYID | BLOCKS_ENCRYPTED | BLOCKS_DECRYPTED | STATUS     | CON_ID |
+------------------+------------+------------------+---------------+-----------+--------------+-------------+------------------+------------------+------------+--------+
| 500005           | T1         | 500012           |               | NO        |              |           0 |                2 |                0 | NORMAL     |   1010 |
+------------------+------------+------------------+---------------+-----------+--------------+-------------+------------------+------------------+------------+--------+
1 row in set
```

可以看到表 `t1` 的 `ENCRYPTED` 字段为 `NO`，说明表 `t1` 解除加密操作成功；

### 参考文档
1. *为新创建的表设置透明加密*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819096)，；
2. *为已有表设置透明加密*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819094)，；
3. *解除加密*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819095)，；


