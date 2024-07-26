---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 安全，高可用，容灾/OceanBase 高可用和容灾/闪回/OceanBase 闪回，回收站管理/","dgPassFrontmatter":true}
---


### OceanBase 闪回，回收站管理 

#### 1 回收站概述
回收站主要用于存储用户删除的数据库和表等信息；

回收站在原理上说就是一个数据字典表，放置用户删除的数据库对象信息；用户删除的东西被放入回收站后，其实仍然占据着物理空间，除非您手动进行清除（ PURGE ）或者对象定期被数据库系统删除；

##### 1.1 回收站支持的对象  
在当前版本中，支持进入回收站的对象有索引、表、库和租户，各模式回收站对象的支持情况如下表所示。

| 模式   | 	索引(Index) |  表(Table) | 数据库(Database) | 租户(Tenant) | 
| :-------------: | :----------: | ------------: |------------: | :------------: |
| MySQL  |  支持  |  支持  |  支持  |  支持 |  
| Oracle  |  支持  |  支持  |  不支持  |  不支持 |  


> `使用限制：`
> 1. 直接 DROP 索引不会进入回收站，删除表时，表上的索引会随主表一起进入回收站；
> 2. 不能对回收站的对象做任何查询和 DML 操作，DDL 操作中也仅支持 Purge 和 Flashback 操作；
> 3. 仅系统租户（sys 租户）支持租户回收站功能；

##### 1.2 数据库、表，索引回收站

##### 1.3 租户回收站  
OceanBase 数据库还支持租户级的回收站对象；  
sys 租户不会被执行 Drop 操作，租户回收站的管理主要由 sys 租户来完成，sys 租户通过执行各类回收站相关的命令来完成对租户回收站的管理；  
租户的回收站对象仅可能出现在 sys 租户下；

> [!NOTE] 注意事项：
> 1. DROP TENANT 操作仅仅是将租户名称变更为统一的回收站中的格式，租户实际占用的空间并没有释放，只是不能再向该租户建立新的连接；
> 2. 执行 PURGE TENANT 时，可以使用原始名称和回收站中的名称；与 FLASHBACK 不同，PURGE TENANT 时，如果多个原始名称相同，则删除的是最早进入回收站中的租户；


#### 2 开启或关闭回收站
租户被创建后，默认回收站为关闭状态；如果开启了回收站，则对数据库对象进行 `Drop` 操作后，对象会进入到回收站；`recyclebin 变量` Global 级别的回收站设置在整个租户内生效，该参数详细情况请查看：[recyclebin](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000354727)，；     

> [!NOTE] OceanBase 数据库的系统变量分为全局变量和 Session 变量：
>1. 全局变量：表示 Global 级别的修改，数据库同一租户内的不同用户共享全局变量。全局变量的修改不会随会话的退出而失效；
>	1. 全局变量修改后，对当前已打开的 Session 不生效，需要重新登录建立的 Session 才能生效；
> 1. Session 变量：表示 Session 级别的修改。当客户端连接到数据库后，数据库会复制全局变量来自动生成 Session 变量。Session 变量的修改仅对当前 Session 生效；


> [!NOTE] 在 OceanBase 数据库中，`sys` 租户和用户租户可管理的回收站对象如下所示：
> 1. `sys` 租户开启回收站后，可以管理的回收站对象包括数据库、表、索引以及租户；
> 2. MySQL 用户租户开启回收站后，可以管理的回收站对象包括数据库、表和索引；
> 3. Oracle 用户租户开启回收站后，可以管理的回收站对象包括表和索引；

```sql
-- 租户管理员登录数据库
SHOW VARIABLES LIKE 'recyclebin';   -- 查看回收站的状态
SHOW VARIABLES LIKE 'recyclebin';   -- 查看回收站的状态

-- 开启或关闭 Global 级别的回收站。Global 级别的回收站设置在整个租户内生效。
-- Global 级别的回收站设置后，对当前 Session 无效，需要重新登录建立新的 Session 才会生效；
SET GLOBAL recyclebin = on;    	-- 开启回收站，on 、1 或 true ：表示开启回收站；
SET GLOBAL recyclebin = off;	-- 关闭回收站，off 、0 或 false：表示关闭回收站；

-- 开启或关闭 Session 级别的回收站，Session 级别的回收站设置仅对当前 Session 有效；
SET @@recyclebin = on;
SET @@recyclebin = off;
```


#### 3 查看回收站中的对象
 
```sql
-- 租户管理员登录数据库 
SHOW RECYCLEBIN;    -- 查看回收站中的对象  
SHOW RECYCLEBIN; 	-- 查看回收站中的对象
/*  
	OBJECT_NAME：表示该对象在回收站中的名称；
	ORIGINAL_NAME：表示原对象名；
	TYPE：表示回收站对象所属的类型；
		MySQL 模式中主要支持 INDEX、TABLE 和 DATABASE 等三种类型；
		Oracle 模式中主要支持 INDEX 和 TABLE 两种类型；  
	CREATETIME：表示该对象进入回收站的时间；  
*/
```

##### 3.1 查看回收站_租户
```sql
-- 1. 使用 root 用户登录到数据库的 sys 租户

-- 2. 查看回收站对象
	SHOW RECYCLEBIN;  -- 查看回收站对象
	/*
		OBJECT_NAME ：表示该对象在回收站中的名称；
		ORIGINAL_NAME ：表示原对象名；
		TYPE：表示回收站对象所属的类型。当前主要支持 INDEX、TABLE 、 DATABASE 和 TENANT 等四种类型。本示例中，mysql 即为一个租户类型的回收站对象；
		CREATETIME：表示该对象进入回收站的时间；
	*/

-- 3. 查看回收站对象
	-- Oracle 租户
	select count(*) from sys.dba_recyclebin where TYPE in ('TABLE','INDEX');   -- 查看数据库中所有回收站的信息
	select count(*) from sys.USER_RECYCLEBIN where TYPE in ('TABLE','INDEX');  -- 查看当前用户拥有的回收站的信息
	
	-- MySql 租户
```
更多情况请参考：[[15_OceanBase/99_内部表介绍/DBA_RECYCLEBIN 表\|DBA_RECYCLEBIN 表]]，[[15_OceanBase/99_内部表介绍/USER_RECYCLEBIN 表\|USER_RECYCLEBIN 表]]，；


#### 4 恢复回收站对象  
被删除的对象进入回收站后，可以使用 `FLASHBACK` 语句恢复回收站对象；

> [!NOTE] `使用注意事项`： 
> 1. 在恢复表时，Flashback 对象的顺序需要符合从属关系，即先 Flashback 数据库，再 Flashback 表；
> 2. 不支持直接恢复索引；在通过 `FLASHBACK` 语句恢复表时，表上的索引也会被恢复；
> 3. 通过 PURGE 命令可以单独清除索引，但是 FLASHBACK 命令不支持单独恢复索引；
> 4. 恢复回收站对象时可修改待恢复对象的名称，但是不能与已有对象重名，否则系统会报错；
> 5. 对于 MySQL 租户，从回收站中恢复表前，如果该表从属的数据库已删除，则需要先恢复数据库再恢复表；
> 6. 若一张表在进入回收站前属于某个表组，通过闪回恢复后该表默认会恢复到该表组中；如果表组已经被删除，则该表恢复后会不属于任何一个表组；

##### 4.1 恢复租户

在 OceanBase 数据库中仅系统租户（sys 租户）支持租户回收站功能；
当 sys 租户的回收站功能开启时，执行 DROP TENANT 操作后，相应的租户默认会进入回收站；
sys 租户的管理员（即 root 用户）可以使用 FLASHBACK 语句将回收站内的租户恢复为正常租户；

```sql
/* 语法：
	FLASHBACK TENANT tenant_name TO BEFORE DROP;       -- 将系统租户回收站中的租户闪回为正常租户
	FLASHBACK TENANT tenant_name TO BEFORE DROP RENAME TO new_tenant_name;
		-- 将系统租户回收站中的 tenant_name 闪回为正常租户，并重命名 new_tenant_name
*/

-- 恢复示例
-- 1. 使用 root 用户登录 sys 租户
	obclient -h10.xx.xx.xx -P2883 -uroot@sys#obdemo -p***** -A
	
	SHOW RECYCLEBIN;  -- 查看回收站中的对象
	
-- 2. 恢复回收站中的租户
-- 将回收站中的租户恢复为正常租户，且恢复后的租户名为进入回收站前的租户名
	-- 语法：FLASHBACK TENANT tenant_name TO BEFORE DROP;   
	FLASHBACK TENANT RECYCLE_$_100017_1672050541224936 TO BEFORE DROP;
		-- tenant_name：可以使用回收站中的名称，也可以使用租户原始的名称；
			-- 由于回收站中的名称全局唯一，因此使用回收站中的名称可以明确恢复的是哪个租户，建议使用回收站中的名称恢复租户；
			-- 如果使用原始名称，当回收站中的多个租户存在相同的原始名称时，此时恢复的租户是这些租户中最后进入回收站中的租户；

-- 将回收站中的租户恢复为正常租户并重命名
	-- 语法：FLASHBACK TENANT tenant_name TO BEFORE DROP RENAME TO new_tenant_name;
	FLASHBACK TENANT RECYCLE_$_100017_1672050541224936 TO BEFORE DROP RENAME TO new_oracle;
	FLASHBACK TENANT Oracle1 TO BEFORE DROP RENAME TO Oracle2;        -- 闪回租户 Oracle1 并重命名为 Oracle2
		-- tenant_name：可以使用回收站中的名称，也可以使用租户原始的名称；
			-- 由于回收站中的名称全局唯一，因此使用回收站中的名称可以明确恢复的是哪个租户，建议使用回收站中的名称恢复租户；
			-- 如果使用原始名称，当回收站中的多个租户存在相同的原始名称时，此时恢复的租户是这些租户中最后进入回收站中的租户；
		-- new_tenant_name：表示重命名的租户名，即该租户从回收站中恢复后的名称；

-- 3. 确认租户已恢复
SELECT * FROM gv$tenant;
```


##### 4.2 恢复数据库

`sys` 租户或 MySQL 用户租户可以从回收站中恢复数据库。恢复时可修改数据库名称，但不能与已有数据库重名，否则系统会报错；

```sql
-- 1. sys 租户或 MySQL 用户租户的租户管理员登录到数据库(默认 MySQL 租户的管理员用户为 root 用户)
obclient -h10.xx.xx.xx -P2883 -uroot@sys#obdemo -p***** -A

SHOW RECYCLEBIN;    -- 获取回收站中对象的名称

-- 2. 恢复回收站中的数据库
-- 从回收站中恢复数据库，恢复后的数据库名为进入回收站前的名称
	-- 语法：FLASHBACK DATABASE object_name TO BEFORE DROP;
	FLASHBACK DATABASE __recycle_$_100017_1673854707660504 TO BEFORE DROP;
		-- object_name：表示数据库对象在回收站中的名称，不支持使用原始名称；执行该语句后，如果待恢复的数据库与租户的已有数据库重名，系统会报错；
	
-- 从回收站中恢复数据库并重命名
	-- 语法：FLASHBACK DATABASE object_name TO BEFORE DROP RENAME TO new_database_name;  
	FLASHBACK DATABASE __recycle_$_100017_1673854707660504 TO BEFORE DROP RENAME TO new_infotest;
		-- object_name：表示数据库对象在回收站中的名称，不支持使用原始名称；
		-- new_database_name：表示从回收站中恢复后数据库的名称；
```


##### 4.3 恢复表

`sys` 租户、 MySQL 用户租户和 Oracle 用户租户均可以从回收站中恢复表；恢复时可修改表的名称，但是不能与已有表重名，否则系统会报错；

OceanBase 数据库的 Oracle 模式仅支持从回收站中恢复被删除的表；

```sql
-- 1. sys 租户或用户租户的租户管理员登录到数据库，默认 MySQL 租户的管理员用户为 root 用户，Oracle 租户的管理员用户为 SYS 用户；
obclient -h10.xx.xx.xx -P2883 -uroot@sys#obdemo -p***** -A

SHOW RECYCLEBIN;    -- 获取回收站中对象的名称

-- 2. 恢复回收站中的表
-- 2.1 从回收站中恢复表，恢复后的表名为进入回收站前的表名
	/* 语法：FLASHBACK TABLE object_name TO BEFORE DROP;
		object_name：表示表对象在回收站中的名称。也可以使用原始名称，由于回收站中的名称全局唯一，建议使用回收站中的名称；
	*/
	FLASHBACK TABLE __recycle_$_100017_1673426335331800 TO BEFORE DROP;
		-- 执行该语句后，恢复后的表名为进入回收站前的名称，且该表从属的数据库或 Schema 对象仍为表删除前从属的数据库或 Schema 对象；
			-- 如果该表进入回收站前的名称与已有表重名，则系统会报错；

-- 2.2 从回收站中恢复表并重命名
	/* 语法：FLASHBACK TABLE object_name TO BEFORE DROP RENAME To new_table_name;
		object_name：表示表对象在回收站中的名称，也可以使用原始名称，由于回收站中的名称全局唯一，建议使用回收站中的名称；
		new_table_name：表示表恢复后重命名的表名；
	*/
	FLASHBACK TABLE __recycle_$_100017_1673426335331800 TO BEFORE DROP RENAME To infotable;
		-- 执行该语句后，恢复后的表名为重命名的表名，且该表从属的数据库或 Schema 对象仍为表删除前从属的数据库或 Schema 对象；
			-- 如果重命名的表名与已有表重名，则系统会报错；

-- 2.3 从回收站中将表恢复到指定的数据库或 Schema 对象并重命名
	/* 语法：FLASHBACK TABLE object_name TO BEFORE DROP RENAME To database_name.new_table_name;
		bject_name：表示表对象在回收站中的名称，也可以使用原始名称，由于回收站中的名称全局唯一，建议使用回收站中的名称；
		database_name.new_table_name：表示表恢复后重命名的表名及其从属的数据库或 Schema 对象；Oracle 租户的 Schema 对象名与用户名同名；
	*/
	FLASHBACK TABLE __recycle_$_100017_1673426335331800 TO BEFORE DROP RENAME To test.infotable;
		-- 执行该语句后，恢复后的表名为重命名的表名，且该表从属的数据库或 Schema 对象为指定的数据库或 Schema 对象。如果重命名的表名与已有表重名，则系统会报错；
```


#### 5 清理回收站
频繁删除数据库对象并重建，会在回收站产生大量的数据，您可以通过清理回收站的方式来清理这些数据；

清理回收站主要有手动和自动两种方式；系统租户（`sys` 租户）可以清理的回收站对象包括 `INDEX`、`TABLE`、`DATABASE` 和 `TENANT` 四种类型，MySQL 用户租户可以清理的回收站对象包括 `INDEX`、`TABLE` 和 `DATABASE` 等三种类型；Oracle 用户租户可以清理的回收站对象包括 `INDEX` 和 `TABLE` 两种类型；


##### 5.1 手动清理回收站
频繁删除数据库对象并重建，会在回收站产生大量数据，这些数据可以通过 `PURGE` 命令清理；

> [!NOTE] `注意事项`：
> 1. Purge 操作会删除对象和从属于该对象的对象（即 Database->Table->Index），即 purge 数据库会删除数据库和从属于该数据库的表和表索引； 
> 2. 将租户从回收站中彻底清除时，租户下的所有对象也会一并被清除，需谨慎执行；
> 3. 当一个对象的上层对象被 Purge，那么当前回收站中关联的下一层对象也会被 Purge；
> 4. 执行 Purge 操作前，建议您通过回收站对待删除的对象进行确认；执行 Purge 操作后，在 OB 数据库的回收站中将再也查询不到对象的信息，真实数据也最终会被作为垃圾回收；


```sql
-- 1. sys 租户或用户租户的租户管理员登录到数据库
	-- MySQL 租户的管理员用户为 root 用户，Oracle 租户的管理员用户为 SYS 用户；
	-- 如果需要清理租户回收站对象，则需要使用 sys 租户登录数据库；
obclient -h10.xx.xx.xx -P2883 -uroot@sys#obdemo -p***** -A

SHOW RECYCLEBIN;    -- 获取回收站中对象的名称

-- 2. 根据业务需要，选择合适的场景，清理回收站
-- 2.1 将租户从回收站中彻底清除
	/* 
	注意事项：将租户从回收站中彻底清除时，租户下的所有对象也会一并被清除；仅 sys 租户支持清理租户回收站对象；
	语法：PURGE TENANT tenant_name;
		tenant_name: 表示回收站中的名称，也可以使用原始名称。使用原始名称时，如果回收站中当前有相同的多个原始名称，则清除的是最早进入回收站中的租户；
	*/ 
	PURGE TENANT oracle001;
	PURGE TENANT RECYCLE_$_100017_1672050541224936;
	PURGE TENANT tenant_name;		-- 将租户从回收站中彻底清除，tenant_name 可以使用原始名称，也可以使用回收站中的名称;
	PURGE RECYCLEBIN;				-- 将所有对象从回收站中彻底清除

-- 2.2 从回收站中物理清理指定的数据库
	/*
	注意事项：仅 sys 租户和 MySQL 用户租户支持清理回收站中的数据库对象；
	语法：PURGE DATABASE object_name;
		object_name：表示该 Schema 对象在回收站中的名称。不支持使用原始名称；
	*/
	PURGE DATABASE __recycle_$_100017_1673854707660504;
	PURGE DATABASE object_name; 	-- 从回收站中物理清理指定的数据库

-- 2.3 从回收站中物理清理指定的表
	/*
	语法：PURGE TABLE object_name;
		object_name：表示该 Schema 对象在回收站中的名称，也可以使用原始名称。使用原始名称时，如果回收站中当前有相同的多个原始名称，则清除的是最早进入回收站中的表；
	*/
	PURGE TABLE __recycle_$_100017_1673426335331800;
	PURGE TABLE object_name;		-- 从回收站中物理清理指定的表

-- 2.4 从回收站中物理清理指定的索引表
	/*
	语法：PURGE INDEX object_name;
		object_name：表示该 Schema 对象在回收站中的名称。不支持使用原始名称；
	*/
	PURGE INDEX __recycle_$_100017_1673426335319344;
	PURGE INDEX object_name;		-- 从回收站中物理清理指定的索引表

-- 2.5 将所有对象从回收站中彻底清除
	PURGE RECYCLEBIN;	-- 清空整个回收站

-- 3. 命令执行成功后，可以再次执行 `SHOW RECYCLEBIN` 语句，确认回收站中的对象是否成功清理
	SHOW RECYCLEBIN;    -- 获取回收站中对象的名称
```


##### 5.2 自动清理回收站
OceanBase 数据库当前支持通过集群级配置项 `recyclebin_object_expire_time` 自动清理收站中过期的 Schema 对象，其默认值为 0s；

1. `recyclebin_object_expire_time = 0`
	1. 值为 `0s` 时表示关闭自动 Purge 回收站功能，值为非 `0s` 时，表示回收一段时间前进入回收站的 Schema 对象；
	2. 值为非 `0s` 时，表示 Purge 一段时间前进入回收站的对象；
	3. 有关集群级配置项的更多信息，请参见 [recyclebin_object_expire_time_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000220568)，；
	
```sql
-- 1. 使用 root 用户登录数据库的 sys 租户
obclient -h10.xx.xx.xx -P2883 -uroot@sys#obdemo -p***** -A

-- 2. 开启/关闭自动清理回收站功能 
show parameters like '%recyclebin_object_expire_time%';   -- 查询回收站保留时长

ALTER SYSTEM SET recyclebin_object_expire_time = "0s";  -- 关闭自动 Purge 回收站功能
ALTER SYSTEM SET recyclebin_object_expire_time = '7d';  -- 打开自动 Purge 回收站功能，并回收 7 天前进入回收站的 Schema 对象

-- 3. 确认回收站中符合条件的对象是否被清理
SHOW RECYCLEBIN;    -- 获取回收站中对象的名称
```


### 参考文档：
1. [回收站概述_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218305)，；
2. [回收站_4.2.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218995)，；

