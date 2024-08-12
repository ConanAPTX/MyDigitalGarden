---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/租户管理/OceanBase 管理租户_查询，创建，修改，删除租户/","dgPassFrontmatter":true}
---


### 查询，创建，修改，删除租户
#### 1 查询租户
```sql
-- 1.使用 root@sys  登录 OceanBase 数据库的 sys 租户 

-- 2.查看当前集群中的租户信息
	-- Oracle 模式普通租户下不支持查询 `gv$tenant` 视图；
SELECT * FROM oceanbase.gv$tenant;

SELECT tenant_id,tenant_name,locality FROM oceanbase.__all_tenant;
```

> [!NOTE] `说明`：
> 1. *MySQL* 模式普通租户下查询，只能查看当前租户的信息；
> 2. *Oracle* 模式普通租户下不支持查询 `gv$tenant` 视图；


#### 2 新建租户
可以通过 SQL 语句或 OCP 来新建租户，推荐使用 OCP 创建租户；
```sql
##### 通过 SQL 语句新建租户
obclient> CREATE TENANT IF NOT EXISTS test_tenant CHARSET='utf8mb4', ZONE_LIST=('zone1','zone2','zone3'), PRIMARY_ZONE='zone1;zone2,zone3', RESOURCE_POOL_LIST=('pool1');

租户创建成功后，默认其管理员用户(MySQL 模式为 root，Oracle 模式为 sys)的密码为空，需要修改管理员用户的密码；

-- 连接 OB
obclient -h10.10.10.1 -P2883 -uusername@tenantname#clustername -p -A 

-- 修改密码
obclient> ALTER USER sqluser01 IDENTIFIED BY 123456;
```

#### 3 修改租户



#### 4 删除租户
可以通过 SQL 语句或 OCP 删除租户；

##### 4.1 通过 SQL 语句删除租户
删除租户后，租户下的数据库和表也同时被删除。但是租户使用的资源配置不会被删除。资源配置可以继续给其他租户使用；只有系统租户的管理员用户(sys 租户的 root 用户)才能执行 DROP TENANT 命令；

当系统租户开启回收站功能时，DROP TENANT 操作表示删除的租户会进入回收站；
当系统租户关闭回收站功能时，DROP TENANT 操作表示延迟删除租户，后台线程会进行 GC（Garbage Collection）动作，租户的信息仍然可以通过内部表查询。租户具体延迟删除的时间由配置项 `schema_history_expire_time` 控制，默认为 7 天，表示执行 DROP TENANT 7 天后，租户会被删除，租户下的数据库和表也同时被删除；该配置项更多信息请参见 [schema_history_expire_time](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944401)，；

```sql
/* 语法：DROP TENANT tenant_name [FORCE | PURGE]
参数：
 PURGE 操作表示仅延迟删除租户，且无论系统租户是否开启回收站功能，删除的租户均不进入回收站；
 FORCE 参数表示无论系统租户是否开启回收站功能，均可以立刻删除租户；
*/
-- 延迟删除租户
	obclient> DROP TENANT t1;  -- 删除租户 t1
		-- 系统租户开启回收站功能后，删除租户 t1，删除的租户可进入回收站；
		-- 系统租户关闭回收站功能时，删除租户 t1，租户被延迟删除；
	obclient> DROP TENANT t1 PURGE;   -- 延迟删除租户 t1，删除的租户不进入回收站

-- 立即删除租户
	obclient> DROP TENANT t1 FORCE;   -- 立刻删除租户 t1
```


### 参考文档