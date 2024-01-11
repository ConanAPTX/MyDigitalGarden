---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/OB 配置管理/OceanBase 管理租户变量/","dgPassFrontmatter":true}
---


### OceanBase 管理租户变量

#### 1 OB 变量分类  
`系统变量`，通常和用户 Session 绑定，用于控制 Session 级别的 SQL 行为；其生效范围：*租户的 Global 级别*，*Session 级别*；  
支持设置 Global 和 Session 级别的变量。设置 Global 级别的系统变量后，当前 Session 上不会生效，新建的任何 Session 都能读到新的变量值；设置 Session 级别的系统变量后，仅对当前 Session 生效；有关系统变量的详细介绍请参见 [设置变量](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944128)；  

> [!NOTE] OceanBase 数据库的系统变量分为 全局变量 和 Session 变量：  
> 1. **全局变量**：表示 Global 级别的修改，数据库同一租户内的不同用户共享全局变量；  
> 	1. 全局变量的修改不会随会话的退出而失效；全局变量修改后，对当前已打开的 Session 不生效，需要重新建立 Session 才能生效；  
> 2. **Session 变量**：表示 Session 级别的修改； 
> 	1. 当客户端连接到数据库后，数据库会复制全局变量来自动生成 Session 变量；Session 变量的修改仅对当前 Session 生效；  
> 3. 名称以 "\_" 开头的变量称为隐藏变量，仅供开发人员在故障排查或紧急运维时使用；如：\_primary_zone_entity_count；


#### 2 查看变量  
##### 2.1 通过 SHOW VARIABLES 语句查询变量
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


##### 2.2 通过 SELECT 语句查询视图查询变量
在本租户相关视图中查看 *Session* / *Global* 级别变量信息；  

```sql
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

有关视图的详细介绍，请参见《系统参考》中的 [系统视图(MySQL 模式)](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946198)，[系统视图(Oracle 模式)](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946142)，；  

#### 3 设置变量  
  
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

### 参考文档  
1. [配置管理介绍](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944126)，；