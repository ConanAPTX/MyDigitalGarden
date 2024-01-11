---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 高可用和容灾/OceanBase 行级访问权限控制/","dgPassFrontmatter":true}
---


### OceanBase 行级访问权限控制

OceanBase 数据库兼容了 Oracle 的 Label Security 功能，可以在行级别对访问进行控制，保证读写数据的安全。基于 Label Security 可以构建对行级数据细粒度访问控制的安全策略。在创建策略后，将该策略应用于需要保护的表，并将标签授予用户；

Label Security 是强制访问控制的一种方式，通过在表中添加一个 Label 列来记录每行的 Label 值，在访问时通过比较用户的 Label 和数据的 Label，达到约束主体（用户）对客体（表中的数据）访问的目的；

OceanBase 数据库提供了内置的安全管理员 LBACSYS 来管理和使用该功能，安全管理员可以通过创建安全策略、定义策略中的 Label、设置用户的 Label，来定制自己的安全策略。一个安全策略可以应用到多张表上，一张表也可以应用多个安全策略。每当一个安全策略被应用，这张表上自动会添加一列，用于该安全策略的访问控制；

> [!NOTE] `使用限制：`
> 目前仅 Oracle 模式支持 Label Security；


#### 1 设置行级访问控制
##### 1.1 解锁 LBACSYS 用户，并赋予权限
```sql
-- 1.使用 sys 用户登录到 Oracle 租户并解锁 LBACSYS 用户
	-- OceanBase 数据库会默认创建 LBACSYS 用户，该用户处于锁定状态，使用 Label Security 功能需要先启用该用户；
	obclient> ALTER USER LBACSYS ACCOUNT UNLOCK;  

-- 2.修改 LBACSYS 用户的登录密码
	-- 为了保障安全性，LBACSYS 用户的密码由 sys 用户指定。如果后续需要修改该密码，重新指定一个新的密码即可；
	obclient> ALTER USER LBACSYS IDENTIFIED BY ***R***;

-- 3.设置 LBACSYS 用户的权限
	--  LBACSYS 用户默认不带任何权限，为了执行行级访问权限相关的操作，需要通过 GRANT 语句给 LBACSYS 用户授予 CREATE SESSION 权限；
	obclient> GRANT CREATE SESSION TO LBACSYS;
```


##### 1.2 创建安全策略
###### 1.2.1 创建安全策略

```sql
-- 4.使用安全管理员 LBACSYS 用户登录到 Oracle 租户；

-- 5.创建安全策略
/* 语法：
	SA_SYSDBA.CREATE_POLICY (
	    policy_name       IN VARCHAR,
	    column_name       IN VARCHAR := NULL,
	    default_options   IN VARCHAR := NULL );
参数解释：
	policy_name：指定策略名称，该名称在数据库中必须是唯一的；策略名称最多可以有 30 个字符，但只有前 26 个字符是有效的，两个策略的策略名称前 26 个字符可能不相同；
		要查找现有策略的列表，请查询 ALL_SA_POLICIES 视图的 POLICY_NAME 列；
	column_name：指定要添加到受策略保护的表中的列的名称；如果为 NULL，则使用 policy_name_COL。两个标签安全策略不能共享同一列名；
	default_options：指定默认强制选项；当应用了策略但未指定该策略所应用的表或 Schema 时，要使用默认强制选项（包括强制选项和隐藏标签列的选项）。用逗号分隔每个选项；
*/
obclient> CALL SA_SYSDBA.CREATE_POLICY('MY_POLICY','MY_LABEL','');  -- 创建了名为 MY_POLICY 的安全策略
```
关于创建标签安全策略的详细信息，参见 [SA_SYSDBA.CREATE_POLICY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221962)，；


###### 1.2.2 创建级别 Level 并指定其短名称和长名称
关于创建级别的详细信息，参见 [SA_COMPONENTS.CREATE_LEVEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221953)，；

```sql
-- 6.创建级别 Level 并指定其短名称和长名称；
/* 语法：
	SA_COMPONENTS.CREATE_LEVEL (
		policy_name       IN VARCHAR,
		level_num         IN NUMBER,
		short_name        IN VARCHAR,
		long_name         IN VARCHAR );
参数解释：
	policy_name：指定策略；若要查找现有策略，请查询 ALL_SA_POLICIES 视图的 POLICY_NAME 列；
	level_num：指定级别编号 (0-9999)；
	short_name：指定级别的短名称（最多 30 个字符）；
	long_name：指定级别的长名称（最多 80 个字符）；
*/
obclient> CALL SA_COMPONENTS.CREATE_LEVEL('MY_POLICY', 100, 'PUBLIC','Public Level');
obclient> CALL SA_COMPONENTS.CREATE_LEVEL('MY_POLICY', 200, 'INTERNAL','Internal Level');
obclient> CALL SA_COMPONENTS.CREATE_LEVEL('MY_POLICY', 300, 'HIGH', 'High Level');
obclient> SELECT * FROM sys.ALL_VIRTUAL_TENANT_OLS_COMPONENT_REAL_AGENT;
+-----------+-----------------------+--------------------+-----------+----------+------------+----------------+-------------+------------------------------+------------------------------+
| TENANT_ID | LABEL_SE_COMPONENT_ID | LABEL_SE_POLICY_ID | COMP_TYPE | COMP_NUM | SHORT_NAME | LONG_NAME      | PARENT_NAME | GMT_CREATE                   | GMT_MODIFIED                 |
+-----------+-----------------------+--------------------+-----------+----------+------------+----------------+-------------+------------------------------+------------------------------+
|      1004 |                500019 |             500018 |         0 |      100 | PUBLIC     | PUBLIC LEVEL   | NULL        | 09-DEC-22 02.38.37.835729 PM | 09-DEC-22 02.38.37.835729 PM |
|      1004 |                500020 |             500018 |         0 |      200 | INTERNAL   | INTERNAL LEVEL | NULL        | 09-DEC-22 02.38.49.336692 PM | 09-DEC-22 02.38.49.336692 PM |
|      1004 |                500021 |             500018 |         0 |      300 | HIGH       | HIGH LEVEL     | NULL        | 09-DEC-22 02.39.02.742233 PM | 09-DEC-22 02.39.02.742233 PM |
+-----------+-----------------------+--------------------+-----------+----------+------------+----------------+-------------+------------------------------+------------------------------+
3 rows in set (0.001 sec)
```


###### 1.2.3 创建数据标签 Label 并查看结果
关于创建数据标签的详细信息，参见 [SA_LABEL_ADMIN.CREATE_LABEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221961)，；

```sql
-- 7.创建数据标签 Label 并查看结果
/* 语法：
	SA_LABEL_ADMIN.CREATE_LABEL (
	    policy_name IN VARCHAR,
	    label_tag   IN BINARY_INTEGER,
	    label_value IN VARCHAR,
	    data_label  IN BOOLEAN := TRUE);
参数解释：
	policy_name，指定现有策略的名称；若要查找现有策略，请查询 ALL_SA_POLICIES 视图的 POLICY_NAME 列；
	label_tag，指定唯一整数值（0-9999999），以表示该标签相对于其他策略标签的排序顺序。此值的长度必须为 1 到 8 位；
	label_value，指定要创建的标签的字符串表示形式，可以使用级别的短名称。您可以通过查询 ALL_SA_LEVELS 视图的 SHORT_NAME 列来找到级别的短名称；
	data_label，如果标签可用于标记行数据，则为 TRUE。使用此选项可将标签定义为对数据有效；
*/
obclient> DELIMITER /
obclient> BEGIN
            SA_LABEL_ADMIN.CREATE_LABEL('MY_POLICY',10000,'PUBLIC',TRUE);
            SA_LABEL_ADMIN.CREATE_LABEL('MY_POLICY',20000,'INTERNAL',TRUE);
            SA_LABEL_ADMIN.CREATE_LABEL('MY_POLICY',30000,'HIGH',TRUE);
          END;
         /
Query OK, 0 rows affected
obclient> DELIMITER ;
obclient> SELECT * FROM sys.ALL_VIRTUAL_TENANT_OLS_LABEL_REAL_AGENT;
+-----------+-------------------+--------------------+-----------+----------+------+------------------------------+------------------------------+
| TENANT_ID | LABEL_SE_LABEL_ID | LABEL_SE_POLICY_ID | LABEL_TAG | LABEL    | FLAG | GMT_CREATE                   | GMT_MODIFIED                 |
+-----------+-------------------+--------------------+-----------+----------+------+------------------------------+------------------------------+
|      1004 |            500022 |             500018 |     10000 | PUBLIC   |    1 | 09-DEC-22 04.25.59.611117 PM | 09-DEC-22 04.25.59.611117 PM |
|      1004 |            500023 |             500018 |     20000 | INTERNAL |    1 | 09-DEC-22 04.25.59.636390 PM | 09-DEC-22 04.25.59.636390 PM |
|      1004 |            500024 |             500018 |     30000 | HIGH     |    1 | 09-DEC-22 04.25.59.651032 PM | 09-DEC-22 04.25.59.651032 PM |
+-----------+-------------------+--------------------+-----------+----------+------+------------------------------+------------------------------+
3 rows in set (0.001 sec)
```


##### 1.3 指定用户的 Label，并应用策略到表上
关于为用户指定标签的详细信息，参见 [SA_USER_ADMIN.SET_LEVELS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221956)，；

```sql
-- 8.指定用户的 Label
	-- 以 sys 用户登录到 Oracle 租户，创建用户 user1 并授予用户 user1 除 GRANT OPTION 以外所有当前用户拥有的权限
	obclient> CREATE USER user1 IDENTIFIED BY *******;
	obclient> GRANT ALL PRIVILEGES TO user1;

	-- 以安全管理员 LBACSYS 登录，为用户 user1 指定 Label；
	obclient> CALL SA_USER_ADMIN.SET_LEVELS('MY_POLICY', 'user1', 'HIGH','PUBLIC', 'PUBLIC', 'PUBLIC');
	obclient [LBACSYS]> SELECT * FROM sys.ALL_VIRTUAL_TENANT_OLS_USER_LEVEL_REAL_AGENT;
	+-----------+------------------------+---------+--------------------+---------------+---------------+---------------+-----------+------------------------------+------------------------------+
	| TENANT_ID | LABEL_SE_USER_LEVEL_ID | USER_ID | LABEL_SE_POLICY_ID | MAXIMUM_LEVEL | MINIMUM_LEVEL | DEFAULT_LEVEL | ROW_LEVEL | GMT_CREATE                   | GMT_MODIFIED                 |
	+-----------+------------------------+---------+--------------------+---------------+---------------+---------------+-----------+------------------------------+------------------------------+
	|      1004 |                 500027 |  500025 |             500018 |           300 |           100 |           100 |       100 | 09-DEC-22 04.31.23.965076 PM | 09-DEC-22 04.31.23.965076 PM |
	+-----------+------------------------+---------+--------------------+---------------+---------------+---------------+-----------+------------------------------+------------------------------+
	1 row in set (0.002 sec)
```


##### 1.4 插入数据，验证查询结果
关于将指定的策略添加到表中的详细信息，参见 [SA_POLICY_ADMIN.APPLY_TABLE_POLICY](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221945)，；

```sql
-- 9.应用策略到表上
	-- 9.1 以 user1 用户登录到 Oracle 租户，创建表 tbl1；
	obclient> CREATE TABLE tbl1(col1 INT,col2 INT);

	-- 9.2 以安全管理员 LBACSYS 登录，将策略 MY_POLICY 应用到表 tbl1 上；
	/* 语法：
		SA_POLICY_ADMIN.APPLY_TABLE_POLICY (
			policy_name       IN VARCHAR,
			schema_name       IN VARCHAR,
			table_name        IN VARCHAR,
			table_options     IN VARCHAR := NULL,
			label_function    IN VARCHAR := NULL,
			predicate         IN VARCHAR := NULL);
	参数解释：
		policy_name：指定策略；要查找现有策略，请查询 ALL_SA_POLICIES 数据字典视图的 POLICY_NAME 列；
		schema_name：受策略保护的表所在的 Schema；
		table_name：受策略保护的表；
		table_options：该表所使用的策略执行选项的列表（以逗号分隔）；
		label_function：字符串调用函数以返回标签值，以用作默认值；
		predicate：附加谓词，用于与基于标签的谓词结合（使用 AND 或 OR）；
	*/
	obclient> CALL SA_POLICY_ADMIN.APPLY_TABLE_POLICY('MY_POLICY','user1', 'tbl1', '', '', '');

-- 11.以 user1 用户登录，在 tbl1 表中插入 col1 和 MY_LABEL 两列数据；
	INSERT INTO tbl1 VALUES(1,1,10000);
	INSERT INTO tbl1(MY_LABEL) VALUES(20000);
	INSERT INTO tbl1(col1) VALUES (2);
	COMMIT;
```

关于设置当前数据库会话标签的详细信息，参见 [SA_SESSION.SET_LABEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221951)，；
关于设置当前数据库会话默认行标签值的详细信息，参见 [SA_SESSION.SET_ROW_LABEL](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000221948)，；

```sql
-- 12. 在 user1 用户下设置 SESSION LABEL 和 ROW LABEL；
	obclient> CALL SA_SESSION.SET_LABEL('MY_POLICY', 'INTERNAL');
	obclient> CALL SA_SESSION.SET_ROW_LABEL('MY_POLICY', 'INTERNAL');
	
	obclient> SELECT SA_SESSION.LABEL('MY_POLICY') FROM DUAL;
		-- 输出：INTERNAL
	obclient> SELECT SA_SESSION.ROW_LABEL('MY_POLICY') FROM DUAL;
		-- 输出：INTERNAL

-- 13.进行查询，可以看到用户 user1 可以访问到 Level 为 INTERNAL 以下级别的数据。
	obclient> SELECT * FROM tbl1;
	+------+------+----------+
	| COL1 | COL2 | MY_LABEL |
	+------+------+----------+
	|    1 |    1 |    10000 |
	| NULL | NULL |    20000 |
	|    2 | NULL |    10000 |
	+------+------+----------+
	3 rows in set

-- 14.修改 Label 后，再次查询，可以看到用户 user1 只能访问到 Level 为 PUBLIC 级别的数据
	obclient> CALL SA_SESSION.SET_LABEL('MY_POLICY', 'PUBLIC');
	obclient> CALL SA_SESSION.SET_ROW_LABEL('MY_POLICY', 'PUBLIC');
	
	obclient> SELECT * FROM tbl1;
	+------+------+----------+
	| COL1 | COL2 | MY_LABEL |
	+------+------+----------+
	|    1 |    1 |    10000 |
	|    2 | NULL |    10000 |
	+------+------+----------+
	2 rows in set
```



### 参考文档
1. [行级访问权限控制](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000218271)，；


