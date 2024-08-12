---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/系统调优/OBProxy 配置参数设置，及调优/","dgPassFrontmatter":true}
---


### OBProxy 配置参数设置，及调优

OBProxy，即 OceanBase Database Proxy，就是 OceanBase 数据库代理 ODP；

#### 1 查询，修改 OBProxy 配置参数

> [!NOTE] ODP 参数可以分为以下四类：
> 1. 支持动态修改的参数此类参数修改后立即生效。详细信息请参考 [支持动态修改的参数](https://www.oceanbase.com/docs/common-odp-doc-cn-1000000000050267)，；
> 2. 不支持动态修改的参数此类参数修改后需要重启 ODP 才能生效。详细信息请参考 [不支持动态修改的参数](https://www.oceanbase.com/docs/common-odp-doc-cn-1000000000050265)，；
> 3. 对普通用户不可见的参数此类参数包括 ODP 内部使用的一些参数以及从通用配置中继承的配置。此类参数仅支持特殊权限用户使用内部命令修改，您无需关注；
> 4. 内存级参数此类参数的修改仅生效一次。详细信息请参考 [内存级参数](https://www.oceanbase.com/docs/common-odp-doc-cn-1000000000050268)，；

```sql
-- 通过 OBProxy 登录 OceanBase 数据库
mysql -h100.64.180.21 -P2889 -uroot@sys#zwy_hz_23032801:100007 -p -A -c -Doceanbase	   -- 通过 OBProxy 连接的方式

show proxyconfig like 'server_tcp%';   -- 查询 OBProxy 配置参数

alter proxyconfig set server_tcp_user_timeout = 0;   -- 修改 OBProxy 配置参数
```

除了可以通过 sql 命令 修改 OBProxy 配置参数，还可以在  ODP 启动时修改配置参数，详细情况：[配置参数说明_V4.2.1](https://www.oceanbase.com/docs/common-odp-doc-cn-1000000000050266)，；

#### 2 OBProxy 参数调优
详细情况：[CPU 相关参数_4.2.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000034075)，[网络传输相关参数_4.2.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000034074)，[节点路由相关参数_4.2.0](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000034073)，；
OBProxy 配置参数详细介绍请参考：[[15_OceanBase/05_OceanBase 性能调优/系统调优/OBProxy 配置参数详细介绍\|OBProxy 配置参数详细介绍]]，；

##### 2.1 OBProxy 生产环境建议使用参数
1. [[15_OceanBase/05_OceanBase 性能调优/系统调优/OBProxy 生产环境建议使用参数\|OBProxy 生产环境建议使用参数]]，详细介绍OBProxy 生产环境建议使用的参数；

##### 2.2 OBProxy 性能调优
OBProxy 作为 OceanBase 数据库的访问代理，其最主要的功能是 SQL 路由；在高并发场景下，是否能快速，高效地路由对应用 SQL 的性能有重要影响；

影响 OBProxy 路由性能的参数主要有：
1. `work_thread_num：`8，工作线程数，对 CPU 占用影响比较大，默认值为 8；更改后需要重启 OBProxy;
2. `automatic_match_work_thread：`，True，根据 Server 的 CPU 个数，自动设置工作线程数；
3. `enable_compression_protocol：`，True，开启 TCP 传输压缩；
4. `proxy_mem_limited:`，2G，OBProxy 的内存限制；


*在实际生产环境，相关参数的设置建议为*：
1. `关于线程数`：
	1. 当 OBProxy 和 OBServer 共同部署时，建议设置为 automatic_match_work_thread = false，指定 work_thread_num = 16/24/32；
		1. OBServer 的 CPU 数通常比较高，如果让 OBProxy 根据 CPU 数自动设置线程数，OBProxy 会分配较大的线程数，很容易导致 OBProxy 与 OBServer 的 CPU 争抢；
		2. 不建议把 work_thread_num 设置为大于 32 的值；过高的 OBProxy 线程数会因为上下文切换而对性能有负面影响；
	2. 当 OBProxy 单独部署时，建议设置为 automatic_match_work_thread = true，让 OBProxy 根据 Server 的 CPU 数来自动设置线程数；
3. `关于内存`：
	1. 当 OBProxy 和 OBServer 共同部署时，需要根据业务量来做相应设置，建议调整 proxy_mem_limited = 4G/8G/12G；
	2. 当 OBProxy 单独部署时，可以设置为所在 Server 内存的 70%；
4. `关于压缩`：
	1. 在 OLTP 环境，SQL 每次查询的记录数有限，基于性能的考虑，建议关闭 TCP 传输的压缩；根据实际测试的结果，关闭压缩可以提高大约 30% 的 OBProxy 转发性能；
	2. 在 OLAP 场景，如果 SQL 执行返回的记录比较多，可以打开 TCP 传输的压缩，提升数据转发的效率；


##### 0.1 路由管理
影响 OBProxy 路由行为的参数主要有：
1. `enable_index_route`，False，全局索引表路由的开关；
2. `enable_reroute`，False，二次路由的开关；
3. `enable_ob_protocol_v2`，False，OceanBase 2.0 传输协议的开关；
	
在 OLTP 场景，建议把以上三个参数全部设置为 True，打开全局索引路由和二次路由；
1. 二次路由：当 OBProxy 因为路由信息不准未把 SQL 路由到正确的 OBServer 节点时，重新路由 SQL 给正确的 OBServer 节点，而不是在错误的节点上进行远程执行；
2. 全局索引路由：当 SQL 查询条件不包含表的分区键，而包含全局索引的键值时，使用全局索引键来进行路由，把 SQL 路由到全局索引所在的 Leader 节点执行；

在 OceanBase V4 之前，OBProxy 无法对于事务中的 SQL 进行自由路由，而是把 SQL 路由到开启事务的 scheduler 角色的 OBServer 上，OceanBase 和 OBProxy 对于只读事务的 SQL 路由做了优化，可以按照 SQL 访问对象的位置进行自由的路由；
1. OBProxy 通过 SQL 回包中的事务标记来判断是否开启了事务，如果开启了事物，则后续的 SQL 都只能路由到上一条 SQL 被路由到的OBServer 节点。
2. 只读事务中的事务标记由 OceanBase 的租户级配置项 ob_proxy_readonly_transaction_routing_policy 控制,其默认值为 True，即只读事务中的 SQL 也带事务标记。我们建议修改改参数为 False，让 OBProxy 只读事务中的 SQL 按照 Leader 节点的位置来路由，减少远程执行的 SQL 数量。

`ob_proxy_readonly_transaction_routing policy`，True，只读事务中的 SQL 是否带事务标记；

以上我们描述的都是强一致读场景下的路由，在弱一致性读场景下 OBProxy 可以根据路由策略按照 LDC 来就近路由，配置方法详见最佳实践系列文章《OceanBase 配置读写分离最住实践》


##### 0.2 链路管理
OBProxy 把应用到数据库的链接分成了两段：一段是应用到 OBProxy 的链路，称为 client session；一段是 OBProxy 到 OBServer 的链路，称为 server session；

因为 OBProxy 可以把同—个 cient session 的 SQL 请求路由到不同的 OBServer 节点，因此一个 client session 可以有多个 server session；

OBProxy 的链路管理主要景 client session 数量的管理，以及 client session 与 server session 的探活和超时处理；

1. OBProxy 所能接受的客户端最大连接数由参数 client max connections 设置，默认值为 8192；正常情况下，用户无需修改最大客户端连接数的限制。如果发生客户端连接教达到 8192 而建连失败的问题，应该首先从应用侧排查应用链接的回收是否正常，是否存在大量未关闭的链接；

2. 关于 TCP 探活与超时参数的设置，建议参照以下配置；
```sql
-- OBProxy 与 OBServer 混部模式下常用的参数配置，
-- TCP 连接参数：client 端
show proxyconfig like 'client_sock_option_flag_out';
alter proxyconfig set client_sock_option_flag_out = 3;  -- 启用 KEEPALIVE，不启用 TCP NO_DELAY
alter proxyconfig set client_sock_option_flag_out = 2;  -- 推荐的配置

show proxyconfig like 'client_tcp%';
alter proxyconfig set client_tcp_user_timeout = 0;		-- 等待 TCP 层 ACK 确认消息的超时时长，0 表示不做超时处理
alter proxyconfig set client_tcp_keepcnt = 2;			-- 最多发送的 KEEPALIVE 探活包个数，2个
alter proxyconfig set client_tcp_keepcnt = 5;           -- 推荐的配置
alter proxyconfig set client_tcp_keepintvl = 5;			-- 两个 KEEPALIVE 探活包的发送时间间隔，5秒
alter proxyconfig set client_tcp_keepidle = 5;			-- 启动 KEEPALIVE 探活前的 idle 时间，5秒

-- TCP 连接参数：server 端
show proxyconfig like 'sock_option_flag_out';
alter proxyconfig SET sock_option_flag_out = 3;		-- 启用 TCP NO_DELAY 和 KEEPALIVE

show proxyconfig like 'server_tcp%';
alter proxyconfig set server_tcp_user_timeout = 0;  -- 等待 TCP 层 ACK 确认消息的超时时长，0 表示不做超时处理
alter proxyconfig set server_tcp_keepcnt = 2;		-- 最多发送的 KEEPALIVE 探活包个数，2个
alter proxyconfig set server_tcp_keepintvl = 5; 	-- 两个 KEEPALIVE 探活包的发送时间间隔，5秒
alter proxyconfig SET server_tcp_keepidle = 5;		-- 启动 KEEPALIVE 探活前的 idle 时间，5秒

-- 
alter proxyconfig set proxy_mem_limited = '4G';
alter proxyconfig set proxy_idc_name = 'beijing';

alter proxyconfig set automatic_match_work_thread = False;
alter proxyconfig set work_thread_num = 32;

alter proxyconfig set enable_compression_protocol = False;
```

> [!NOTE] 注意：
> 1. 在以上示例中，我们把 TCP USER TIMEOUT 设置为0，即关闭超时处理，其目的是为了防止在网络不稳定时ACK应答丢失导致的超时断连。关闭 TCP USER TIMEOUT 是我们的推荐配置，可以有效规避一些链接中断的问题；
> 2. 在以上示例中，我们对 client session 和 server session 均开启了 KEEPALIVE 探活，理论上可以在 15s 后发现处于假活状态的链接，并主动关闭这类问题链接；
> 3. 在以上示例中，我们对 client session 关闭了 TCP NO DELAY，即和许把多个小的数据包聚合成一个后发送给 client，目的是提升 SQL查询结果的传输效率；而对 server session 我们开启了 TCPNO_DELAY，即对收到的每一个client 请求（比如SQL）都及时发送给数据库，目的是提升 SQ1的执行效率；


### 参考文档：

1. [OBProxy 配置参数说明_V4.2.1](https://www.oceanbase.com/docs/common-odp-doc-cn-1000000000050266)，；


