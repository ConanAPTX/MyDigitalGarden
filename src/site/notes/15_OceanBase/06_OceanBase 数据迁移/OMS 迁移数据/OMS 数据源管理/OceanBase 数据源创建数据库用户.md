---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/OMS 迁移数据/OMS 数据源管理/OceanBase 数据源创建数据库用户/","dgPassFrontmatter":true}
---



### OceanBase 数据源创建数据库用户
#### 1 创建 OceanBase 数据库用户
详细情况：[[15_OceanBase/06_OceanBase 数据迁移/OMS 迁移数据/OMS 数据源管理/OMS 数据源用户权限说明_01\|OMS 数据源用户权限说明_01]]，；

#### 2 创建 DRC 用户
若需要读取 OceanBase 数据库的【*增量日志数据*】和【*数据库对象结构信息*】，请在`源端 sys 租户`下创建 DRC 用户；

此处创建的 DRC 用户，用于创建 OceanBase 数据源（包括物理数据源和逻辑数据源）时填写 *高级选项* 中的 *DRC 用户用户名*;

```sql
CREATE USER <drc_user> IDENTIFIED BY '<drc_password>'; 
GRANT SELECT ON oceanbase.* TO <drc_user>;
```


#### 3 创建 sys 租户用户


#### 4 创建 \_\_oceanbase_inner_drc_user 用户
若需要迁移无【*主键表*】，执行数据迁移项目前，请根据实际情况，创建用户并赋予权限；

若源端为 OceanBase 数据库 MySQL 租户，请在【*源端业务租户*】下创建 `__oceanbase_inner_drc_user` 用户；
```sql
-- OceanBase 数据库 MySQL 租户
CREATE USER __oceanbase_inner_drc_user IDENTIFIED BY '<password>';
GRANT SELECT ON *.* TO __oceanbase_inner_drc_user;
```

若源端为 OceanBase 数据库 Oracle 租户，请在【*源端业务租户*】下创建 `__OCEANBASE_INNER_DRC_USER` 用户；
```sql
-- OceanBase 数据库 Oracle 租户
-- 创建用户
CREATE USER '__OCEANBASE_INNER_DRC_USER' IDENTIFIED BY <your_password>;

-- 赋予权限
	-- V2.2.77 之前版本
	GRANT CREATE SESSION TO '__OCEANBASE_INNER_DRC_USER';
	GRANT SELECT ON *.* TO '__OCEANBASE_INNER_DRC_USER';

	-- V2.2.77 及之后版本
	GRANT CREATE SESSION TO '__OCEANBASE_INNER_DRC_USER';
	GRANT SELECT ANY DICTIONARY TO '__OCEANBASE_INNER_DRC_USER';

	-- 对迁移库表的 `SELECT` 权限，支持以下两种赋权方式：
		-- 赋予系统权限  
		GRANT SELECT ANY TABLE TO '__OCEANBASE_INNER_DRC_USER';
		
		-- 赋予对象权限（仅支持对具体库表对象赋权）  
		GRANT SELECT ON <schema>.<table> TO '__OCEANBASE_INNER_DRC_USER';
```



### 参考文档



