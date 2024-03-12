---
{"number headings":"auto, first-level 3, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/OB 配置管理/OceanBase 管理配置项，租户变量/","dgPassFrontmatter":true}
---


## OceanBase 管理配置项，租户变量
### 1 OceanBase 管理配置项，租户变量概述  
#### 1.1 OceanBase 配置项概述
`系统配置项`，其生效范围：集群、租户、Zone、机器；主要用于运维，常用于控制机器及其以上级别的系统行为；可以作为 OBServer 启动参数和租户创建参数，也可以在 OBServer 运行时进行修改以调整系统行为。 有关配置项的详细介绍请参见 [设置参数](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944127)，；  

配置项的生效方式分为：`动态生效`，`重启生效`；大部分配置项为动态生效方式，即不需要重启 OBServer 即可生效；  

OceanBase 数据库的配置项分为【*集群级配置项*】和【*租户级配置项*】；通过配置项的设置可以控制整个集群的负载均衡、合并时间、合并方式、资源分配和模块开关等功能。详情请参见 [系统配置项总览](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946151)，；集群级配置项，作用范围为整个集群所有 OBServer；有关集群级配置项的介绍，请参见 [集群级配置项](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946158)，； 租户级配置项，作用范围为当前租户在集群内所在的 OBServer；有关租户级配置项的介绍，请参见 [租户级配置项](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945167)；  

#### 1.2 OceanBase 租户变量概述
`系统变量`，通常和用户 Session 绑定，用于控制 Session 级别的 SQL 行为；其生效范围：*租户的 Global 级别*，*Session 级别*；  
支持设置 Global 和 Session 级别的变量。设置 Global 级别的系统变量后，当前 Session 上不会生效，新建的任何 Session 都能读到新的变量值；设置 Session 级别的系统变量后，仅对当前 Session 生效；有关系统变量的详细介绍请参见 [设置变量](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944128)；  

> [!NOTE] OceanBase 数据库的系统变量分为 全局变量 和 Session 变量：  
> 1. **全局变量**：表示 Global 级别的修改，数据库同一租户内的不同用户共享全局变量；  
> 	1. 全局变量的修改不会随会话的退出而失效；全局变量修改后，对当前已打开的 Session 不生效，需要重新建立 Session 才能生效；  
> 2. **Session 变量**：表示 Session 级别的修改； 
> 	1. 当客户端连接到数据库后，数据库会复制全局变量来自动生成 Session 变量；Session 变量的修改仅对当前 Session 生效；  
> 3. 名称以 "\_" 开头的变量称为隐藏变量，仅供开发人员在故障排查或紧急运维时使用；如：\_primary_zone_entity_count；


### 2 OceanBase 管理配置项
#### 2.1 查看 OceanBase 集群配置项  
可以查看集群中除隐藏配置项（\_xx\_xx 格式的配置项）以外的所有配置项及其所有属性描述；详细情况：[查询，设置参数](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355579)，；  
  
1. `系统租户`：可以查看本租户的租户级配置项和集群级配置项信息。同时，可以通过指定 `TENANT` 关键字来查看指定租户的配置项信息；  
2. `普通租户`：可以查看本租户的租户级配置项以及系统租户的集群级配置项信息；  
3. WHERE expr 中可以指定的列属性与 SHOW PARAMETERS 返回结果中的列属性一致；  

```sql  
/*  语法：SHOW PARAMETERS [LIKE 'pattern' | WHERE expr] [TENANT = tenant_name]  
参数：  
返回结果中的列属：  
	zone：所在的 Zone；  
	svr_type：机器类型；  
	svr_ip：机器 IP；  
	svr_port：机器端口；  
	name：配置项名；  
	data_type：配置项数据类型，包括 NUMBER 、STRING 、CAPACITY 等；  
	value：配置项值；说明：由于在修改配置项值时，支持修改指定 Zone 或 Server 的配置项值，故不同 Zone 或 Server 对应的配置项的值可能不同；
	info：配置项的说明信息；  
	section：配置项所属的分类：  
		SSTABLE：表示 SSTable 相关的配置项； OBSERVER：表示 OBServer 相关的配置项；  ROOT_SERVICE：表示 RootService 相关的配置项；
		TENANT：表示租户相关的配置项；       TRANS：表示事务相关的配置项；            LOAD_BALANCE：表示负载均衡相关的配置项。  
		DAILY_MERGE：表示合并相关的配置项；  CLOG：表示 Clog 相关的配置项；           LOCATION_CACHE：表示 Location Cache 相关的配置项。  
		CACHE：表示缓存相关的配置项；        RPC：表示 RPC 相关的配置项；             OBPROXY：表示 OBProxy 相关的配置项。  
	scope：配置项范围属性；  
		TENANT，表示该配置项为租户级配置项；CLUSTER，表示该配置项为集群级配置项；  
	source：当前值来源；TENANT；CLUSTER；CMDLINE；OBADMIN；FILE；DEFAULT；  
	edit_level：定义该配置项的修改行为：  
		READONLY：表示该参数不可修改。  
		STATIC_EFFECTIVE：表示该参数可修改但需要重启 OBServer 才会生效；  
		DYNAMIC_EFFECTIVE：表示该参数可修改且修改后动态生效；
*/  

-- 系统租户(即sys租户)和普通租户均可以查询集群级配置项的值  
SHOW PARAMETERS LIKE 'large_query_threshold';  
SHOW PARAMETERS WHERE scope = 'CLUSTER'; -- 查询集群级配置项  
SHOW PARAMETERS WHERE scope = 'CLUSTER' and name = 'data_dir'; -- 查询集群级配置项  
SHOW PARAMETERS WHERE scope = 'TENANT' and name = 'audit_trail'; -- 查询租户级配置项  
SHOW PARAMETERS WHERE svr_ip != 'XXX.XXX.XXX.XXX';  
SHOW PARAMETERS WHERE INFO like '%ara%';

-- 系统租户和普通租户查看本租户的租户配置项
SHOW PARAMETERS LIKE 'clog_max_unconfirmed_log_count';
SHOW PARAMETERS WHERE svr_ip='xx.xx.xx.xx' AND name='clog_max_unconfirmed_log_count';

-- 系统租户查看指定租户的租户配置项
SHOW PARAMETERS LIKE 'clog_max_unconfirmed_log_count' TENANT='mytenant';
SHOW PARAMETERS WHERE svr_ip='xx.xx.xx.xx' AND name='clog_max_unconfirmed_log_count' TENANT='mytenant';
```

还可以通过查询 `GV$OB_PARAMETERS` 视图查询配置项，与 SHOW 语句不同，通过该视图可以查看集群中的所有配置项（含隐藏配置项）及其所有属性描述；有关该视图的更多信息，请参见 [GV$OB_PARAMETERS（MySQL 模式）](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000034188)，*该视图在 V4.0.0 版本中新增*；

```sql  
-- 查看集群配置项(该视图 V4.0.0 版本中新增*)
SELECT * FROM GV$OB_PARAMETERS WHERE NAME LIKE '%syslog_level%';  
```  

#### 2.2 修改 OceanBase 配置项  

租户参数即租户级配置项，系统租户（即 sys 租户）和普通租户都可以设置租户级配置项。系统租户可以设置本租户和其他租户的租户级配置项，普通租户只能设置本租户的租户级配置项；

> [!NOTE] `说明：`  
> 1. 同时修改多个配置项时，以逗号（,）分隔；
> 2. 租户级配置项可以直接在本租户下修改，也可以在系统租户下通过指定 TENANT 关键字来修改；
> 3. ALTER SYSTEM 语句不能同时指定 Zone 和 OBServer。并且在指定 Zone 时，仅支持指定一个 Zone；指定 OBServer 时，仅支持指定一个 OBServer。如果修改时，不指定 Zone 也不指定 OBServer，则集群级配置项会在整个集群所有 OBServer 上生效；租户级配置项会在当前租户在集群内所在的 OBServer 上生效；
> 4. 确认一个配置项为集群级别还是租户级别，可根据 SHOW PARAMETERS 语句执行结果中的 scope 列对应的值来判断；
> 	1. scope 值为 CLUSTER 则表示为集群级配置项；
> 	2. scope 值为 TENANT 则表示为租户级配置项； 

```sql  
-- MySQL 模式
/*  
语法：  
	ALTER SYSTEM [SET] parameter_name = expression  [SCOPE = {SPFILE | BOTH}] 
		[COMMENT [=] 'text']  
		[TENANT [=] ALL|tenant_name]  
		{SERVER [=] 'ip:port' | ZONE [=] 'zone'};  
参数说明：  
	parameter_name：指定要修改的配置项名称。  
	expression：指定修改后的配置项的值。  
	COMMENT 'text'：用于添加关于本次修改的注释。该参数为可选，建议不要省略。  
	SCOPE：指定本次配置项修改的生效范围，默认为 BOTH。  
		SPFILE：表示只修改内部表中的配置项值，当 OBServer 重启以后才生效。仅重启生效的配置项支持。  
		BOTH：表示既修改内部表中的配置项值，又修改内存中的配置项值，修改立即生效，且 OBServer 重启以后配置值仍然生效。  
	TENANT：用于在系统租户下，修改所有或指定租户的租户级配置项。  
		ALL：所有租户。  
		tenant_name：指定租户。  
	SERVER：只修改指定 Server 实例的某个配置项。  
	ZONE：表明本配置项的修改针对指定集群的特定 Server 类型，否则，针对所有集群的特定 Server 类型； 
*/  

-- 1.系统租户设置集群配置项
	obclient> ALTER SYSTEM SET clog_max_unconfirmed_log_count = 1600;
	obclient> ALTER SYSTEM SET clog_max_unconfirmed_log_count = 400; 
	obclient> ALTER SYSTEM SET clog_max_unconfirmed_log_count = 400, auto_broadcast_location_cache_rate_limit = 500;

 -- 2. 系统租户设置租户配置项   
	ALTER SYSTEM SET clog_max_unconfirmed_log_count = 400 TENANT='mytenant';   -- 系统租户设置租户 mytenant 的配置项 
	ALTER SYSTEM SET clog_max_unconfirmed_log_count = 400 ZONE='z1';          -- 修改指定 Zone 的 clog_max_unconfirmed_log_count 配置项
	ALTER SYSTEM SET clog_max_unconfirmed_log_count = 400 SERVER='XXX.XXX.XXX.XXX:XXXXX';  -- 修改指定 OBServer 的 clog_max_unconfirmed_log_count 配置项
	ALTER SYSTEM SET clog_max_unconfirmed_log_count = 400 TENANT='ALL';  -- 在系统租户下，修改所有租户级配置项 clog_max_unconfirmed_log_count
	ALTER SYSTEM SET clog_max_unconfirmed_log_count = 400 TENANT='Oracle';  -- 在系统租户下，修改指定租户的租户级配置项 clog_max_unconfirmed_log_count 
	 
-- 3.普通租户的租户管理员设置本租户的配置项
	obclient> ALTER SYSTEM SET clog_max_unconfirmed_log_count=1600;
	obclient> ALTER SYSTEM SET clog_max_unconfirmed_log_count=1600 SCOPE=SPFILE;
	obclient> ALTER SYSTEM SET clog_max_unconfirmed_log_count=1600 SERVER='192.168.100.1:2882';
	obclient> ALTER SYSTEM SET clog_max_unconfirmed_log_count=1600 ZONE='z1';

-- Oracle 模式 
/*  语法：ALTER SYSTEM SET parameter_name = expression  
参数说明：  
	parameter_name：指定要修改的配置项名称；  
	expression：指定修改后的配置项的值；  
*/  
 
-- 租户管理员设置本租户的配置项
obclient> ALTER SYSTEM SET clog_max_unconfirmed_log_count = 1600;
obclient> ALTER SYSTEM SET clog_max_unconfirmed_log_count = 400;   -- 修改 clog_max_unconfirmed_log_count 配置项 

```  


### 3 OceanBase 管理租户变量
#### 3.1 查询 OceanBase 变量
```sql
/*
语法：
  SHOW [SESSION] VARIABLES [LIKE 'pattern' | WHERE expr]   -- 查询 Session 级别变量
  SHOW GLOBAL VARIABLES [LIKE 'pattern' | WHERE expr]      -- 查询 Global 级别变量
参数：
  pattern 表示变量关键字；
*/

obclient> SHOW VARIABLES LIKE 'ob_query_timeout';
obclient> SHOW SESSION VARIABLES LIKE 'ob_query_timeout';
obclient> SHOW GLOBAL VARIABLES WHERE variable_name LIKE 'ob_query_timeout';

SHOW VARIABLES LIKE 'ob_query_timeout';  
SHOW VARIABLES WHERE VARIABLE_NAME = 'ob_query_timeout';  
SHOW GLOBAL VARIABLES LIKE 'ob_query_timeout';  
SHOW GLOBAL VARIABLES WHERE VARIABLE_NAME = 'ob_query_timeout';  
SHOW GLOBAL VARIABLES WHERE VARIABLE_NAME LIKE 'ob_query_timeout';  
```

还可以通过 SELECT 语句查询系统视图查询变量，有关视图的详细介绍，请参见《系统参考》中的 [系统视图(MySQL 模式)](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946198)，[系统视图(Oracle 模式)](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946142)，；  

```sql
-- 在本租户相关视图中查看 *Session* / *Global* 级别变量信息
-- 1. MySQL 租户下的系统变量相关视图如下：
	-- INFORMATION_SCHEMA.SESSION_VARIABLES：记录当前租户下的 Session 变量信息；  
	SELECT * FROM INFORMATION_SCHEMA.SESSION_VARIABLES WHERE VARIABLE_NAME = 'ob_query_timeout';
  
	-- INFORMATION_SCHEMA.GLOBAL_VARIABLES：记录当前租户下的全局变量信息；  
	SELECT * FROM INFORMATION_SCHEMA.GLOBAL_VARIABLES WHERE VARIABLE_NAME = 'ob_query_timeout';
	
-- 2. Oracle 租户下的系统变量相关视图如下：  
	-- SYS.TENANT_VIRTUAL_SESSION_VARIABLE：记录当前租户下的 Session 变量信息；  
	SELECT * FROM SYS.TENANT_VIRTUAL_SESSION_VARIABLE WHERE VARIABLE_NAME = 'ob_query_timeout';
	  
	-- SYS.TENANT_VIRTUAL_GLOBAL_VARIABLE：记录当前租户下的全局变量信息；  
	SELECT * FROM SYS.TENANT_VIRTUAL_GLOBAL_VARIABLE WHERE VARIABLE_NAME = 'ob_query_timeout';
```


#### 3.2 设置 OceanBase 变量  
  
> [!NOTE] `设置变量注意事项：`
> 1. 设置 Session 级别的变量仅对当前 Session 有效，对其他 Session 无效；
> 2. MySQL 模式设置 Global 级别变量要求当前用户拥有 SUPER 权限或 ALTER SYSTEM 权限；
> 3. 设置 Global 级别的变量对当前 Session 无效，需要重新登录建立新的 Session 才会生效；
> 4. Oracle 模式还可以通过 ALTER SYSTEM SET VARIABLE_NAME = 'VALUE'; 语句设置 Global 级别的变量；

```sql  
/*  
语法：  
	SET [SESSION] VARIABLE_NAME = 'VALUE';   -- 设置 Session 级别变量
	SET GLOBAL VARIABLE_NAME = 'VALUE';      -- 设置 Global 级别变量 
*/  
  
-- 通过 SQL 语句设置 Session/Global 级别变量  
-- MySQL 模式  
obclient> SET ob_query_timeout = 20000000;           -- 设置 Session 级别变量  
obclient> SET SESSION ob_query_timeout = 20000000;   -- 设置 Session 级别变量
obclient> SET GLOBAL ob_query_timeout = 20000000;    -- 设置 Global 级别变量  

obclient> SET @@foreign_key_checks = ON;
obclient> SET @@foreign_key_checks = 1;

-- Oracle 模式  
obclient> SET ob_query_timeout = 20000000; -- 设置 Session 级别变量  
obclient> SET GLOBAL ob_query_timeout = 20000000; -- 设置 Global 级别变量  
obclient> ALTER SESSION SET ob_query_timeout = 20000000; -- 设置 Session 级别变量  
obclient> ALTER SYSTEM SET ob_query_timeout = 20000000; -- 设置 Global 级别变量  
```  

对于全局变量、只读变量，我们在创建租户时也能对其进行设置；有关创建租户语法的详细信息，请参见 [CREATE TENANT](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944130)，；

## 参考文档  
1. [配置管理介绍_V 3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944126)，[系统配置项概述_V 3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355399)，；  
2. [查看集群配置项_V 4.2.0](https://yuque.antfin.com/szy01716982/wycdh6/tfgm4vkraeyrzuwm#y2Clq)，；
3. [配置管理介绍](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944126)，；


