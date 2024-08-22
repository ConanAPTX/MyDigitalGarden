---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/系统调优/secure_file_priv 配置项/","dgPassFrontmatter":true}
---



### secure_file_priv 配置项

#### 1 secure_file_priv 介绍
1. `secure_file_priv`：控制导入或导出到文件时可以访问的路径；仅 DBA 可以设置该变量，其他人无法设置；【该变量从 V2.2.77 版本开始引入】
	 1. 【*GLOBAL*】【varchar】；默认值为 NULL；根据需要设置，一般保持默认值；
	 2. 从 V4.2.1 版本开始默认值由 `NULL` 调整为 `””`，即调整为空值；
	 3. 从 V4.2.0 版本开始，枚举值`空字符串`和 `NULL` 的含义由原来的`无限制`调整为`导入、导出被禁用`；
	 4. 该参数可以有以下几个值：
		 1. `V3.2.4`： *空字符串 / NULL*：没有限制，任意路径均可以访问；*路径名*：仅该路径下的文件可以被导入或导出；*"NULL"*：表示导入、导出被禁用；
		 2. `V4.3.2`：*空字符串 / NULL / "NULL"*：表示导入、导出被禁用；*路径名*：表示仅该路径下的文件可以被导入或导出；*"/"*：表示没有限制，任意路径均可；
	 5. 详细情况：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944616)，[V4.3.2](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001052763)，；


#### 2 设置 secure_file_priv 配置项 
用户需要授予权限才能访问机器上文件，操作步骤如下：

1. 使用 `root` 用户登录到数据库的 `sys` 租户，设置集群级配置项 `secure_file_priv`，配置导入文件时可以访问的路径。
```sql
-- 查询变量
SHOW VARIABLES  like 'SECURE_FILE_PRIV';    -- sys 租户和所有用户租户均可以查询
```

在 V3.2.4 BP5 及之后版本，由于安全原因，只能使用通过本地 Unix Socket 连接的 Client 执行修改该全局变量的 SQL 语句；

```sql
-- 1.在 V3.2.4 BP5 及之后版本：
	obclient -S /home/admin/oceanbase/run/sql.sock -u******@obtenant -p******
	/* 参数说明：
		-S：提供 本地 ObServer 节点的 Unix Socket 文件路径，位于本地 ObServer 安装目录下的run/sql.sock，默认 Unix Socket 文件路径为 `/home/admin/oceanbase/run/sql.sock`；
		-u：提供租户的连接账户，格式为：用户名@租户名。
		通过 Unix Socket 方式连接时，只能连接本地 OBServer 节点，同时也需要确保该租户的资源分布在本地 OBServer 节点上，
			如果该租户的资源未分布在本地 OBServer 节点上，则无法通过本地 OBServer 节点连接到该租户；
	*/

SET GLOBAL SECURE_FILE_PRIV = "/home/admin";
```

> [!NOTE] 注意：
> 由于安全原因，以上 SQL 只能使用本地访问执行，不能使用远程 obclient 执行。即需要在 observer 所在机器上登录 obclient （或者 MySQL 客户端） 执行；

```sql
-- 2. 对用户进行授权
GRANT FILE ON *.* TO user_name;       -- 授予用户 file 权限命令如下，`user_name` 是需要执行 LOAD DATA 命令的用户；

-- 3. 对待导入文件所在的目录进行授权
chmod -R 755 /home/admin/test         -- 假设导入文件所在的目录为 `/home/admin/test`；
```



### 参考文档



