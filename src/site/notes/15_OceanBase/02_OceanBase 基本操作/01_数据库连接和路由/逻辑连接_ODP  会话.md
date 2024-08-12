---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/01_数据库连接和路由/逻辑连接_ODP  会话/","dgPassFrontmatter":true}
---


### 逻辑连接_ODP 会话

#### 1 展示全部 Session
本文介绍了如何查看 ODP 上所有租户连接的全部 Client Session 的内部状态的方法；

`sys` 租户通过 `SHOW PROXYSESSION` 语句可以查看 ODP 上所有租户连接的全部 Client Session 的内部状态；用户租户通过 `SHOW PROXYSESSION` 语句可以查看 ODP 上当前租户连接的全部 Client Session 的内部状态；

```sql 
-- 1. 通过 ODP 连接的方式登录 OceanBase 集群的 `sys` 租户
	obclient -h10.xx.xx.xx -uroot@sys#obdemo -P2883 -p****** -c -A oceanbase

-- 2. 查看 ODP 上租户连接的全部 Client Session 的内部状态；
	SHOW PROXYSESSION;
		-- sys 租户通过 `SHOW PROXYSESSION` 语句可以查看 ODP 上所有租户连接的全部 Client Session 的内部状态；
		-- 用户租户通过 `SHOW PROXYSESSION` 语句可以查看 ODP 上当前租户连接的全部 Client Session 的内部状态；
/*
各字段含义如下表所示：
	proxy_sessid：OceanBase 数据库内标记每个 OBProxy 的 64 位 ID 号；
	Id：ODP 内部标记每个 Client 的 `id` 号，即 `cs id`；
	Cluster：集群名；
	Tenant：租户名；
	User：用户名；
	Host：用户 IP 地址和端口号；
	db：数据库名；
	trans_count：OBProxy 会话已传输的事务数量；
	svr_session_count：OBProxy 维持的会话数量；
	state：网络连接状态；
	tid：线程 ID；
	pid：进程 ID；
*/
```


> [!NOTE] 说明：
> `Id` 为 ODP 内部标记每个 Client 的 `id` 号，即 `cs id`，`proxy_sessid` 为整个 OceanBase 数据库标记每个 Client 的 64 位 `id` 号，与标准的 `CONNECTION_ID()` 不一致。有关 `CONNECTION_ID` 的详细介绍，请参见 [CONNECTION_ID](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946053)，；

#### 2 展示 Session 详细状态
本文介绍了如何查看 ODP 上指定 Client Session 的详细内部状态。

##### 2.1 操作步骤
`sys` 租户和用户租户可以通过 `SHOW PROXYSESSION ATTRIBUTE` 语句查看指定 Client Session 的详细内部状态，包括该 Client Session 上涉及的相关 Server Session；

```sql
-- 1. 通过 ODP 连接的方式连接 OceanBase 数据库
obclient -h10.xx.xx.xx -uusername@obtenant#obdemo -P2883 -p****** -c -A oceanbase
```
有关更加详细的通过 ODP 连接方式连接数据库的操作指引，请参见 [通过 OBClient 连接数据库](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944890)，；
    
```sql
-- 2. 查看指定 Client Session 的详细内部状态
/*
SQL 语句如下：
	SHOW PROXYSESSION ATTRIBUTE [id [like 'xxx']]
参数说明：
	不指定 `id` 时，显示当前 Session 的详细状态(ODP 1.1.0 版本起开始支持)，支持模糊查询当前 Session 指定属性名称的 value (proxy 1.1.2 版本起开始支持)。
	指定 `id` 时，显示指定 ID 对应 Client Session 的详细状态(ODP 1.1.0 版本起开始支持)，支持模糊查询指定属性名称的 value (ODP 1.1.2 版本起开始支持)。
	`id` 既可以是 `cs_id`，也可以是 `CONNECTION_ID`，显示结果相同。
		`cs_id` 为 ODP 内部标记的每个 Client 的 `id` 号，`CONNECTION_ID` 为整个 OceanBase 数据库标记的每个 Client 的 `id` 号。
		MySQL 模式下的 `connection_id` 通过 `SELECT CONNECTION_ID();` 语句获取，
		Oracle 模式下的 `connection_id` 通过 `SHOW FULL PROCESSLIST;` 语句获取。
	like 模糊匹配字段名称，支持 '%' 和 '_';
*/

-- 示例如下：
SHOW PROXYSESSION;    -- 查询当前 Session 的详细状态
+--------------------+------+-----------------+--------+------+-----------------+------+-------------+-------------------+-------------------+---------+---------+
| proxy_sessid       | Id   | Cluster         | Tenant | User | Host            | db   | trans_count | svr_session_count | state             | tid     | pid     |
+--------------------+------+-----------------+--------+------+-----------------+------+-------------+-------------------+-------------------+---------+---------+
| 756006681247547396 |    2 | ob1.jianhua.sjh | sys    | root | 127.0.0.1:22540 | NULL |           0 |                 1 | MCS_ACTIVE_READER | 2230520 | 2230520 |
+--------------------+------+-----------------+--------+------+-----------------+------+-------------+-------------------+-------------------+---------+---------+
1 row in set

SHOW PROXYSESSION ATTRIBUTE;
SHOW PROXYSESSION ATTRIBUTE 2 like '%id%';
SHOW PROXYSESSION ATTRIBUTE 2147549201 like '%id%';
+------------------------+---------------------+----------------+
| attribute_name         | value               | info           |
+------------------------+---------------------+----------------+
| proxy_sessid           | 756006681247547396  | cs common      |
| cs_id                  | 2                   | cs common      |
| cluster                | ob1.jianhua.sjh     | cs common      |
| tenant                 | sys                 | cs common      |
| user                   | root                | cs common      |
| host_ip                | 127.0.0.1           | cs common      |
| host_port              | 22540               | cs common      |
| db                     | NULL                | cs common      |
| total_trans_cnt        | 0                   | cs common      |
| svr_session_cnt        | 1                   | cs common      |
| active                 | true                | cs common      |
| read_state             | MCS_ACTIVE_READER   | cs common      |
| tid                    | 2230520             | cs common      |
| pid                    | 2230520             | cs common      |
| modified_time          | 1469435190244371115 | cs stat        |
| reported_time          | 1469435190244371115 | cs stat        |
| hot_sys_var_version    | 0                   | cs var version |
| sys_var_version        | 0                   | cs var version |
| user_var_version       | 0                   | cs var version |
| last_insert_id_version | 0                   | cs var version |
| db_name_version        | 0                   | cs var version |
| server_ip              | xx.xx.xx.xx         | last used ss   |
| server_port            | 13203               | last used ss   |
| server_sessid          | 2147549201          | last used ss   |
| ss_id                  | 4                   | last used ss   |
| state                  | MSS_KA_CLIENT_SLAVE | last used ss   |
| transact_count         | 2                   | last used ss   |
| server_trans_stat      | 0                   | last used ss   |
| hot_sys_var_version    | 0                   | last used ss   |
| sys_var_version        | 0                   | last used ss   |
| user_var_version       | 0                   | last used ss   |
| last_insert_id_version | 0                   | last used ss   |
| db_name_version        | 0                   | last used ss   |
+------------------------+---------------------+----------------+
33 rows in set
```


```text
各字段含义如下表所示：
	attribute_name：属性名称；
	value：属性值；
	info：基本信息；

常见的属性及其说明如下表所示：
	proxy_sessid：OBProxy 的会话 ID 号；
	cluster：所属集群名；
	socket_fd：套接字描述符；
	tenant：租户；
	user：用户；
	host_ip：用户 IP；
	host_port：用户端口号；
	db：数据库；
	total_trans_cnt：传输事务的总数量；
	svr_session_cnt：会话总数量；
	active：是否存活；
	read_state：客户端会话的状态；
	tid：线程 ID；
	pid：进程 ID；
	modified_time：历史修改时间；
	reported_time：历史报告时间；
	hot_sys_var_version：热更新的系统变量版本；
	sys_var_version：系统变量版本；
	user_var_version：用户变量版本；
	last_insert_id_version：最后插入 ID 版本；
	db_name_version：数据库名的版本；
	server_ip：OBServer 的 IP 地址；
	server_port：OBServer 的端口号；
	server_sessid：OBServer 的会话 ID 号；
	ss_id：OBProxy 标记 Server Session的 ID 号；
```


#### 3 终止 Server Session
`sys` 租户可以通过 `KILL PROXYSESSION` 语句终止 ODP 上指定的 Session，包括 *Client Session* 和 *Server Session*；

```shell
-- 1. 通过 ODP 连接的方式登录 OceanBase 集群的 `sys` 租户
obclient -h10.xx.xx.xx -uroot@sys#obdemo -P2883 -p****** -c -A oceanbase
```
有关更加详细的通过 ODP 连接方式连接数据库的操作指引，请参见 [通过 OBClient 连接数据库](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944890)，；

##### 3.1 终止指定 Client Session
```sql
-- 终止指定的 Client Session：
	KILL PROXYSESSION {cs_id | connection_id};
/*
参数说明：
	cs id 为 ODP 内部标记的每个 Client 的 `id` 号，`connection_id` 为整个 OceanBase 数据库标记的每个 Client 的 `id` 号；
	与 KILL connection_id 的作用一致；
*/
```
有关 `KILL` 语句的详细介绍，请参见 [KILL（MySQL 模式）](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945551) 或 [KILL（Oracle 模式）](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000947109)，；

```sql
obclient> SHOW PROXYSESSION;
+--------------------+------+-----------------+--------+------+-----------------+------+-------------+-------------------+-------------------+---------+---------+
| proxy_sessid       | Id   | Cluster         | Tenant | User | Host            | db   | trans_count | svr_session_count | state             | tid     | pid     |
+--------------------+------+-----------------+--------+------+-----------------+------+-------------+-------------------+-------------------+---------+---------+
| 756006681247547400 |    7 | ob1.jianhua.sjh | sys    | root | 127.0.0.1:23706 | NULL |           0 |                 1 | MCS_ACTIVE_READER | 2230520 | 2230520 |
+--------------------+------+-----------------+--------+------+-----------------+------+-------------+-------------------+-------------------+---------+---------+
1 row in set

obclient> KILL PROXYSESSION 7;
ERROR 1317 (70100): Query execution was interrupted
obclient> SHOW PROXYSESSION;
ERROR 2006 (HY000): MySQL server has gone away
No connection. Trying to reconnect...
Connection id:    8
Current database: *** NONE ***

+--------------------+------+-----------------+--------+------+-----------------+------+-------------+-------------------+-------------------+---------+---------+
| proxy_sessid       | Id   | Cluster         | Tenant | User | Host            | db   | trans_count | svr_session_count | state             | tid     | pid     |
+--------------------+------+-----------------+--------+------+-----------------+------+-------------+-------------------+-------------------+---------+---------+
| 756006681247547401 |    8 | ob1.jianhua.sjh | sys    | root | 127.0.0.1:23715 | NULL |           0 |                 1 | MCS_ACTIVE_READER | 2230520 | 2230520 |
+--------------------+------+-----------------+--------+------+-----------------+------+-------------+-------------------+-------------------+---------+---------+
1 row in set

obclient> SELECT CONNECTION_ID ();
+-----------------+
| connection_id() |
+-----------------+
|      2147549203 |
+-----------------+
1 row in set

obclient> KILL PROXYSESSION 2147549203;
ERROR 1317 (70100): Query execution was interrupted
obclient> SHOW PROXYSESSION;
ERROR 2006 (HY000): MySQL server has gone away
No connection. Trying to reconnect...
Connection id:    9
Current database: *** NONE ***

+--------------------+------+-----------------+--------+------+-----------------+------+-------------+-------------------+-------------------+---------+---------+
| proxy_sessid       | Id   | Cluster         | Tenant | User | Host            | db   | trans_count | svr_session_count | state             | tid     | pid     |
+--------------------+------+-----------------+--------+------+-----------------+------+-------------+-------------------+-------------------+---------+---------+
| 756006681247547402 |    9 | ob1.jianhua.sjh | sys    | root | 127.0.0.1:23734 | NULL |           0 |                 1 | MCS_ACTIVE_READER | 2230520 | 2230520 |
+--------------------+------+-----------------+--------+------+-----------------+------+-------------+-------------------+-------------------+---------+---------+
1 row in set
```
通过指定 `cs_id` 或 `CONNECTION_ID` 来 KILL 当前的 Session 时，当前的连接中断，命令执行成功。使用 `SHOW PROXYSESSION` 查看时，OBClient 会重新建立 Session 连接，并执行 SQL 显示结果；

您可以通过 `KILL PROXYSESSION (cs_id | connection_id) ss_id` 语句来终止指定 Client Session 上的 Server Session；

 
##### 3.2 终止指定 Client Session 上的 Server Session
终止指定 *Client Session* 上的 *Server Session*；
```sql
-- 语法：
	KILL PROXYSESSION {cs_id | connection_id} ss_id;
-- 参数说明：        
    id 既可以是 `cs_id`，也可以是 `connection_id`，显示结果相同；        
	    cs_id` 为 ODP 内部标记的每个 Client 的 `id` 号，`connection_id` 为整个 OceanBase 数据库标记的每个 Client 的 `id` 号；
	ss_id 表示 ODP 内部标记每个 Server 端会话（Server Session）的 `id` 号，可以从 `SHOW PROXYSESSION ATTRIBUTE id` 中获取；
```
详细的获取操作请参见 [展示 Session 详细状态](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944882)，；

```sql
obclient> SHOW PROXYSESSION;
+----------------------+-------+----------+--------+------+----------------------+------+-------------+-------------------+-------------------+-------+-------+-----------+
| proxy_sessid         | Id    | Cluster  | Tenant | User | Host                 | db   | trans_count | svr_session_count | state             | tid   | pid   | using_ssl |
+----------------------+-------+----------+--------+------+----------------------+------+-------------+-------------------+-------------------+-------+-------+-----------+
| 12402504630519660556 | 64940 | test420  | sys    | root | 100.xx.xx.xx:63882   | NULL |           0 |                 1 | MCS_ACTIVE_READER | 76286 | 76286 |         0 |
+----------------------+-------+----------+--------+------+----------------------+------+------------- +-------------------+-------------------+-------+-------+-----------+
1 row in set

obclient> SHOW PROXYSESSION ATTRIBUTE 64940;
+----------------------------------+----------------------+----------------+
| attribute_name                   | value                | info           |
+----------------------------------+----------------------+----------------+
| proxy_sessid                     | -6044239443189891060 | cs common      |
| cs_id                            | 64940                | cs common      |
| cluster                          | test3233             | cs common      |
| tenant                           | sys                  | cs common      |
| user                             | root                 | cs common      |
| host_ip                          | 100.xx.xx.xx         | cs common      |
| host_port                        | 63882                | cs common      |
| db                               | NULL                 | cs common      |
| total_trans_cnt                  | 0                    | cs common      |
| svr_session_cnt                  | 1                    | cs common      |
| active                           | true                 | cs common      |
| read_state                       | MCS_ACTIVE_READER    | cs common      |
| tid                              | 76286                | cs common      |
| pid                              | 76286                | cs common      |
| idc_name                         |                      | cs common      |
| modified_time                    | 0                    | cs stat        |
| reported_time                    | 0                    | cs stat        |
| hot_sys_var_version              | 0                    | cs var version |
| sys_var_version                  | 2                    | cs var version |
| user_var_version                 | 0                    | cs var version |
| last_insert_id_version           | 0                    | cs var version |
| db_name_version                  | 0                    | cs var version |
| server_ip                        | xx.xx.xx.xx          | last used ss   |
| server_port                      | 2881                 | last used ss   |
| server_sessid                    | 3221579563           | last used ss   |
| ss_id                            | 16                   | last used ss   |
| state                            | MSS_KA_CLIENT_SLAVE  | last used ss   |
| transact_count                   | 2                    | last used ss   |
| server_trans_stat                | 0                    | last used ss   |
| hot_sys_var_version              | 0                    | last used ss   |
| sys_var_version                  | 2                    | last used ss   |
| user_var_version                 | 0                    | last used ss   |
| last_insert_id_version           | 0                    | last used ss   |
| db_name_version                  | 0                    | last used ss   |
| is_checksum_supported            | 1                    | last used ss   |
| is_safe_read_weak_supported      | 0                    | last used ss   |
| is_checksum_switch_supported     | 1                    | last used ss   |
| checksum_switch                  | 1                    | last used ss   |
| enable_extra_ok_packet_for_stats | 1                    | last used ss   |
+----------------------------------+----------------------+----------------+
39 rows in set

obclient> KILL PROXYSESSION 64940 16;
```




#### 4 查看网络连接的内部属性状态
通过 `SHOW PROXYNET CONNECTION` 语句查看 ODP 上所有网络连接的内部属性状态；

```sql
-- 语法：
	SHOW PROXYNET CONNECTION [thread_id [LIMIT xx]]
-- 参数：
	-- thread_id：线程 ID，不指定 thread_id 时，展示 ODP 上所有网络连接的内部属性状态；指定 thread_id 时，仅展示指定 thread 上的连接状态；
    -- LIMIT xx：支持指定 LIMIT [offset,] rows 和 LIMIT rows OFFSET offset 参数，格式与 MySQL 完全兼容。当 rows == -1 时，展示全部行；

obclient> SHOW PROXYNET CONNECTION\G       -- 查看 ODP 上所有网络连接的内部属性状态；
obclient> SHOW PROXYNET CONNECTION 0\G     -- 查看 ODP 上线程 ID 为 0 的网络连接的内部属性状态；
obclient> SHOW PROXYNET CONNECTION 0 limit 2 offset 1\G    -- 查看 ODP 上线程 ID 为 0 的网络连接的内部属性状态，并返回从第二行开始的两行记录；

/*
各字段含义如下表所示：
	thread_id：线程 ID；
	connect_id：网络连接 ID；
	socket_fd：套接字描述符；
	type：连接状态；
	src_ip：源 IP；
	src_port：源端口；
	dst_ip：目的 IP；
	dst_port：目的端口；
	virtual_ip：虚拟 IP；
	virtual_port：虚拟端口；
	bind_style：绑定类型；
	read_enabled：是否可读；
	read_nbytes：需要读取的数据字节数；
	read_ndone：已经读取的数据字节数；
	write_enabled：是否可写；
	write_nbyte：需要发送的数据字节数；
	write_ndone：已经发送的数据字节数；
	alive_time(sec)：存活时间；
	activity_timeout_in(sec)：活跃超时；
	inactivity_timeout_in(sec)：不活跃超时时长；
	timeout_close_in(sec)：超时关闭时长；
	last_error_no：最近出错的错误号；
	shutdown：已断开的网络连接；
	comments：注释；
*/
```


### 3 参考文档
1. *展示全部 Session*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944881)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000818927)，；
2. *展示 Session 详细状态*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944882)，；
3. *展示 Session 统计项*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944883)，；
4. *展示 Session 变量*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944884)，；
5. *终止 Server Session*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944885)，；
6. *网络连接*：[odp_v4.3.0](https://www.oceanbase.com/docs/common-odp-doc-cn-1000000000755427)，查看 ODP 上所有网络连接的内部属性状态；


