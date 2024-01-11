---
{"number headings":"auto, first-level 3, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/租户管理/OceanBase 租户会话管理/","dgPassFrontmatter":true}
---


## OceanBase 租户会话管理
### 1 查询租户会话  
SHOW PROCESSLIST 语句的显示结果与连接数据库的方式有关。当通过 ODP 连接数据库时，显示的是对应的 ODP 节点上的会话信息；当通过直连方式连接数据库时，显示的是租户对应的 OBServer 节点上的会话信息；  
查看租户会话时，租户管理员可以查看当前租户内的所有会话信息，普通用户只能查看当前自己的会话信息。如果您拥有 PROCESS 权限，则可以查看租户内的所有会话；  
```sql  
-- 查看租户会话信息(用户登录数据库的 MySQL 租户或 Oracle 租户)  
SHOW PROCESSLIST; -- 通过直连方式连接数据库时，使用 SHOW PROCESSLIST 语句查看租户会话  
/* 返回结果中：  
Id：表示该会话的 ID；  
User：表示该会话所属的用户；  
Host：表示发起该会话的客户端 IP 及端口号；  
db：表示该会话当前连接的数据库名。Oracle 模式显示为与用户名同名的 Schema 名；  
Command：表示该会话正在执行的命令类型；  
Time：表示当前命令执行的时间，单位为秒。如果命令发生了重试，则系统会清零后重新计算；  
State：表示该会话当前的状态；  
	MCS_INIT(初始化)；MCS_ACTIVE_READER(激活)；MCS_KEEP_ALIVE(保活)；MCS_HALF_CLOSE(半关闭)；MCS_CLOSED(已关闭)；  
Info：表示该会话执行的语句；  
*/  
  
-- 通过直连方式或 ODP 连接数据库时，使用 SHOW FULL PROCESSLIST 语句查看租户会话  
SHOW FULL PROCESSLIST;  
/*  
ID：表示该会话的 ID；  
USER：表示该会话所属的用户；  
TENANT：表示该会话所访问的租户名称；  
HOST：表示发起该会话的客户端 IP 及端口号；  
如果您是通过 ODP 连接的数据库，则表示 ODP 的主机 IP 及端口号；  
DB：表示该会话当前连接的数据库名。Oracle 模式显示为与用户名同名的 Schema 名；  
COMMAND：表示该会话正在执行的命令类型；  
TIME：表示当前命令执行的时间，单位为秒。如果命令发生了重试，则系统会清零后重新计算  
STATE：表示该会话当前的状态；  
	MCS_INIT(初始化)；MCS_ACTIVE_READER(激活)；MCS_KEEP_ALIVE(保活)；MCS_HALF_CLOSE(半关闭)；MCS_CLOSED(已关闭)；  
INFO：表示该会话执行的语句；  
IP：表示该会话所属的服务器 IP 地址，即 OBServer 节点的 IP 地址；  
PORT：表示该会话所属的服务器的 SQL 端口号，即 OBServer 节点的 SQL 端口号；  
*/  
```  

除了上述方法，还可以通过 OCP 查看租户会话信息，及会话统计，详细情况：[查看租户会话](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355595)，；  

若在 OB V4.x 版本还可以通过 GV$OB_PROCESSLIST 视图查询租户会话；  
租户管理员可以查看当前租户内的所有会话信息，普通用户只能查看当前自己的会话信息；如果您拥有 PROCESS 权限，则可以查看所有会话；  

```sql  
-- 查看租户会话  
-- 用户登录集群的 MySQL 租户或 Oracle 租户  
SELECT * FROM oceanbase.GV$OB_PROCESSLIST; -- MySQL 模式  
SELECT * FROM SYS.GV$OB_PROCESSLIST; -- Oracle 模式  
/*  
	SVR_IP：表示该会话所属的服务器的 IP 地址，即 OBServer 节点的 IP 地址。  
	SVR_PORT：表示该会话所属的服务器的 RPC 端口号，即 OBServer 节点的 RPC 端口号。  
	SQL_PORT：表示该会话所属的服务器的 SQL 端口号，即 OBServer 节点的 SQL 端口号。  
	ID：表示该会话 ID。  
	USER：表示该会话所属的用户。  
	HOST：通过直连方式连接数据库时，表示发起该会话的客户端 IP 及端口号。  
		如果是通过 ODP 连接数据库，则表示 ODP 的主机 IP 及端口号。  
	DB：表示该会话当前连接的数据库名。Oracle 模式显示为与用户名同名的 Schema 名。  
	TENANT:表示该会话所访问的租户名称。  
	COMMAND：表示该会话正在执行的命令类型。  
	TIME：表示当前命令执行的时间，单位为秒。如果命令发生了重试，则系统会清零后重新计算。  
	TOTAL_TIME：表示当前命令执行的总时间，单位是秒。如果命令发生了重试，系统不会清零。  
	STATE：表示该会话当前的状态。  
	INFO：表示该会话正在执行的语句。  
	PROXY_SESSID：如果是通过 ODP 连接数据库，则本列显示的是 ODP 节点上的 Session ID。否则显示为 NULL。  
	MASTER_SESSID：  
		如果是通过 ODP 连接数据库，则本列显示的是 ODP 节点上的主 Session ID，用于串联同一个 SQL 的多个子 Session。否则显示为 NULL；  
	USER_CLIENT_IP：发起该会话的客户端 IP。  
	USER_HOST：发起该会话的客户端主机名。  
	RETRY_CNT：当前命令的重试次数。  
	RETRY_INFO：当前命令的重试信息，一般为最后一次重试的错误码。  
	SQL_ID：当前命令对应的 SQL ID 信息。  
	TRANS_ID：事务 ID。  
	THREAD_ID：线程 ID。如果是通过 ODP 连接数据库，则该线程 ID 也表示 ODP 节点上的 Session ID。  
	SSL_CIPHER：加密密码名称。  
	TRACE_ID：Trace ID；  
	TRANS_STATE：事务状态；  
	ACTION：通过调用 DBMS_APPLICATION_INFO.SET_ACTION Procedure 设置的当前执行操作的名称；  
	MODULE：通过调用 DBMS_APPLICATION_INFO.SET_MODULE Procedure 设置的当前执行操作的名称；  
	CLIENT_INFO：通过 DBMS_APPLICATION_INFO.SET_CLIENT_INFO 过程设置的信息；  
*/  
```

### 2 终止租户会话  

OceanBase 数据库支持通过 KILL 语句终止会话，详细情况：[终止租户会话](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355596)，；  
若拥有 PROCESS 权限，则可以查看所有会话；若拥有 SUPER 权限，则可以逐一终止所有会话和语句；否则，只能查看和终止自己的会话和语句；管理员可以逐一终止所有会话和语句；

```sql  
/*  
语法：KILL [CONNECTION] 'session_id';  
语句使用说明：KILL 语句每次只能终止一个会话；  
*/  
SHOW PROCESSLIST; -- 查看租户会话  
SHOW FULL PROCESSLIST; -- 查看租户会话  
  
KILL session_id; -- 终止指定的会话  
KILL CONNECTION session_id; -- 终止指定的会话  
	-- 其中，session_id 可通过 SHOW PROCESSLIST 语句获取；  
```  
更多 KILL 语句的使用和说明，请参见 [KILL（MySQL 模式）](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945551) 和 [KILL（Oracle 模式）](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000947109)；


### 3 设置租户最大连接数  
在 OceanBase 2.x，3.x 版本中不能设置控制租户最大连接数；  
在 OceanBase 4.x 版本中是支持该需求，对于 MySQL 租户，可通过 max_connections / max_user_connections 变量实现，对于 Oracle 租户，可通过隐藏配置项 `_resource_limit_max_session_num` 实现；  
设置租户最多连接数的更多详情：[设置租户最大连接数](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000033320)，；  
若当前租户同时设置了`_resource_limit_max_session_num`、`max_user_connections` 和 `max_connections` 的值，则对于某个普通用户，其并发连接数只要达到其中一个值，即无法再建立新的连接；对于管理员用户，其并发连接数只要达到 `max_user_connections` 或 `max_connections` 的其中一个值，即无法再建立新的连接；  
  
1. `max_connections`：用于设置整个租户的最大连接数；该配置项在 2.x，3.x 版本不生效；  
	1. 【==GLOBAL==】【uint】；默认值为 2147483647，取值范围为: [1, 2147483647]；  
	2. 该变量修改后，需要重启 OBServer 节点才能生效；  
	3. 该变量仅在 4.x 版本的 MySQL 租户生效；详细情况：[设置租户变量 max_connections 无效](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000207666?back=kb) ，；  
2. `max_user_connections`：用于设置租户内单个用户的最大连接数；该配置项在 2.x，3.x 版本不生效；  
	1. 【==GLOBAL==，==SESSION==】【uint】；取值范围为: [0, 4294967295]，默认值为 0，即表示不限制连接数；  
	2. 该变量仅在 4.x 版本的 MySQL 租户中生效；  
	3. 该变量修改后，需要重启 OBServer 节点才能生效；  
	4. 注意事项：该变量可通过 SET GLOBAL 语句修改 Global 生效方式下的取值，不可通过 ALTER SESSION SET 语句修改 Session 生效方式下的取值；  
3. `_resource_limit_max_session_num`：设置租户内普通用户总的最大并发连接数；  
	1. 【==租户级==】默认值为 0，取值范围为 [0,1000000]；动态生效；  
	2. 当该隐藏配置项的值大于 0 时，以实际设置的值为准；当该隐藏配置项的值为 0 时，系统内部会自动按照一定的规则计算出最大连接数；  
	4. 具体计算规则如下：用户租户并发连接数上限 = MAX( 100, 租户内存 * 5% / (100 KB) ）  
		1. 租户内存由创建租户时分配的资源池中资源单元的内存决定；100 KB 为根据经验计算出的单个会话可能占用的内存；100 为连接数下限；
		2. 根据该算法，如果租户的内存为 2 GB，则该租户的最大连接数为 MAX( 100, 2 GB * 5% / (100 KB)）= 1000；  
	6. 该配置项仅在 4.x 版本中生效；(待确定)  
	7. 该配置项的值对 sys 租户或用户租户的管理员用户（MySQL 模式默认为 root 用户，Oracle 模式默认为 SYS 用户）不受限；  
	
```sql  
-- 查看整个租户的最大连接数(仅支持 MySQL 租户)  
SHOW VARIABLES LIKE 'max_connections';  
SELECT * FROM INFORMATION_SCHEMA.SESSION_VARIABLES WHERE VARIABLE_NAME = 'max_connections';  
SET GLOBAL max_connections = 1000; -- 设置整个租户的最大连接数  
-- 执行成功后，需要重启 OBServer 节点才能生效；  
  
-- 查看租户内单个用户的最大并发连接数(仅支持 MySQL 租户)  
SHOW VARIABLES LIKE 'max_user_connections';  
SELECT * FROM INFORMATION_SCHEMA.SESSION_VARIABLES WHERE VARIABLE_NAME = 'max_user_connections';  
SET GLOBAL max_user_connections = 50; -- 设置租户内单个用户的最大并发连接数  
-- 执行成功后，需要重启 OBServer 节点才能生效；  
  
-- 查看租户内普通用户总的最大并发连接数  
-- 隐藏配置项无法通过 SHOW 语句查询，仅支持通过 GV$OB_PARAMETERS 视图查询；  
SELECT * FROM oceanbase.GV$OB_PARAMETERS WHERE NAME LIKE '_resource_limit_max_session_num';  
SELECT * FROM SYS.GV$OB_PARAMETERS WHERE NAME LIKE '_resource_limit_max_session_num';  
ALTER SYSTEM SET _resource_limit_max_session_num = 100; -- 设置租户内普通用户总的最大并发连接数  
```  
有关视图 `GV$OB_PARAMETERS` 的更多介绍，请参见 [GV$OB_PARAMETERS](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000034573)，；  


## 参考文档
1. 查看租户会话：[V3.2.3](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355595)，[V4.2.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000033317)，；
2. [终止租户会话](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355596)，；  
3. [设置租户最大连接数](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000033320)，；



