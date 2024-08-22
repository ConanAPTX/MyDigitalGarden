---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/从文件迁移数据到 OB 数据库/load data 命令/","dgPassFrontmatter":true}
---


### 使用 LOAD DATA 语句导入数据
OceanBase 数据库支持通过 LOAD DATA 命令加载外部文件的数据到数据库表中；

#### 1 LOAD DATA 命令概述
LOAD DATA 目前可以对 CSV 格式的文本文件进行导入，整个导入的过程如下：
1. 【`解析文件`】：OceanBase 会根据用户输入的文件名，读取文件中的数据，并且根据用户输入的并行度来决定并行或者串行解析输入文件中的数据；
2. 【`分发数据`】：
	1. 由于 OceanBase 是分布式数据库系统，各个分区的数据可能分布在各个不同的 OBServer 上，LOAD DATA 会对解析出来的数据进行计算，决定数据需要被发送到哪个 OBServer；
3. 【`插入数据`】：当目标 OBServer 收到了发送过来的数据之后，在本地执行 INSERT 操作把数据插入到对应的分区当中；

> [!NOTE] 使用注意事项：
> 1. 带有触发器（Trigger）的表禁止使用 `LOAD DATA` 语句；


##### 1.1 load data 修改记录
*在 V3.2.4 版本中*：该版本不支持 `LOCAL INFILE` 的语法加载数据文件, 计划后续版本将支持该功能。OceanBase 数据库中的 `LOAD DATA` 语句仅支持加载本地直连的 OceanBase 数据库的输入文件，用户需要在导入之前将文件拷贝当前 OBServer 节点上；

*在 V4.0.0 版本中*：CSV 文件必须在位于 OBServer 本地，当前版本(V4.0.0)不支持从远程客户端加载数据；

*在 V4.1.0 版本中*：该版本开始 `LOAD DATA` 语句支持加载 OSS 和 OBServer 节点上的 OceanBase 数据库的输入文件；

*在 V4.2.4 版本中*：该版本中开始支持使用 `LOAD DATA LOCAL INFILE` 语法加载本地数据文件；OceanBase 数据库 MySQL 模式从 V4.2.2 版本起，开始支持使用 `LOAD DATA LOCAL INFILE` 语法加载本地数据文件。并且在执行 `LOAD DATA LOCAL INFILE` 命令时，系统会自动添加 `IGNORE` 选项；
OceanBase 数据库支持加载位于 OSS、服务器端（OBServer 节点）和客户端（本地）的数据文件；


#### 2 获取 LOAD DATA 执行权限
要从外部文件导入数据，您需要具有 `FILE` 权限及以下设置：
1. 执行 load data 的数据库用户需要 *FILE* 权限；
2. 加载服务器端文件时，需要提前设置系统变量 *secure_file_priv*，配置导入或导出文件时可以访问的路径；

##### 2.1 赋予数据库用户权限

```sql
-- 1. 对用户进行授权 FILE 权限
GRANT FILE ON *.* TO user_name;       -- user_name 是需要执行 LOAD DATA 命令的用户；

-- 2. 授予其他必要权限
	-- 2.1 MySQL 模式需要拥有对应表的 `INSERT` 权限；
	GRANT INSERT ON database_name.tbl_name TO user_name;
		-- database_name 是数据库名称；tbl_name 是表名；user_name 是需要执行 LOAD DATA 命令的用户；

	-- 2.2 Oracle 模式需要拥有 `CREATE SESSION` 权限
	GRANT CREATE SESSION TO user_name;    -- `user_name` 是要授予权限的用户名
```

##### 2.2 secure_file_priv 配置项
设置 _secure_file_priv_ 配置项详细情况：[[15_OceanBase/05_OceanBase 性能调优/系统调优/secure_file_priv 配置项\|secure_file_priv 配置项]]，；
```sql
-- 1.登录到要连接的 OBServer 节点。
ssh admin@xxx.xxx.xxx.xxx

-- 2.执行以下命令，通过本地 Unix Socket 连接方式连接租户 `mysql001`。
obclient -S /home/admin/oceanbase/run/sql.sock -uroot@mysql001 -p******
 
-- 3.设置导出路径为 `/home/admin`；
SHOW VARIABLES like 'SECURE_FILE_PRIV';        -- sys 租户和所有用户租户均可以查询
obclient [(none)]> SET GLOBAL secure_file_priv = "/home/admin";
Query OK, 0 rows affected

-- 4. 对待导入文件所在的目录进行授权
chmod -R 755 /home/admin/test         -- 假设导入文件所在的目录为 `/home/admin/test`；
```


#### 3 load data 日志文件
若导入的过程中出现了错误，出现错误的 INSERT 语句会被回滚，并且 LOAD DATA 语句会在 OBServer 安装路径的 log 子目录下产生名称为 `obloaddata.log.<XXXXXX>` 的日志文件；

以下是一个日志文件的内容示例，日志中会包含 LOAD DATA 产生的任务的基本信息，包含租户名、输入文件名、目标表名、并行度、使用的 LOAD DATA 命令，并且以行为单位给出具体错误的信息；

```txt
Tenant name:    mysql
File name:  /home/admin/a.csv
Into table: `test`.`t`
Parallel:   1
Batch size: 1000
SQL trace:  YD7A20BA65670-0005AADAAA3C****
Start time: 2020-07-29 21:08:13.073741
Load query:
load data infile '/home/admin/test.csv' into table t fields terminated by ',' lines terminated by '\n'
Row ErrCode ErrMsg
1   1062    Duplicated primary key
2   1062    Duplicated primary key
```


### 参考文档

1. *LOAD DATA*：
	1. `MySQL`：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945552)，[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000885012)，[V4.1.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001701133)，；
	2. `Oracle`：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-1000000000286469)，[V4.1.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702700)，；
2. _使用 LOAD DATA 语句导入数据_：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000943981)，[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-0000000001078699)，[V4.2.4](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001033136)，；
3. *使用 LOAD DATA 语句旁路导入数据*：[V4.2.4](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001033146)，；
