---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/系统调优/OBProxy 慢查询日志/","dgPassFrontmatter":true}
---


### OBProxy 慢查询日志

OBProxy 有自己的慢查询日志打印功能，通过 OBProxy 的以下两个配置项可控制打印到日志中的 SQL 或事务的处理时间阈值；

1.  `slow_proxy_process_time_threshold` / `slow_query_time_threshold` / `slow_transaction_time_threshold`

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/05-ocean-base//ob-proxy/#97f540" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



77. `slow_proxy_process_time_threshold` / `slow_query_time_threshold` / `slow_transaction_time_threshold` 

</div></div>


一般修改配置项 *slow_transaction_time_threshold* 即可，配置项 *slow_proxy_process_time_threshold* 默认值为 2ms，该值适用于绝大多数场景；

```sql
-- 通过 OBProxy 登录 OceanBase 数据库 
mysql -h100.64.180.21 -P2889 -uroot@sys#zwy_hz_23032801:100007 -p -A -c -Doceanbase 

-- 通过 OBProxy 连接的方式 
show proxyconfig like 'slow_transaction_time_threshold%'; -- 查询 OBProxy 配置参数
show proxyconfig like 'slow_proxy_process_time_threshold%'; 

obclient> ALTER PROXYCONFIG SET slow_transaction_time_threshold='100ms';
obclient> ALTER PROXYCONFIG SET slow_proxy_process_time_threshold='5ms';
```

#### 1 OBProxy 慢查询日志解析
```note
[2016-05-24 22:39:00.824392] WARN [PROXY.SM] update_cmd_stats (ob_mysql_sm.cpp:4357) [28044][Y0-7F70BE3853F0] [14]
Slow query:

client_ip=127.0.0.1:17403 // 执行SQL client IP
server_ip=100.81.152.109:45785 // SQL被路由到的observer
conn_id=2147549270
cs_id=1
sm_id=8
cmd_size_stats=
	client_request_bytes:26 // 客户端请求 SQL大小
	server_request_bytes:26 // 路由到observer SQL大小
	server_response_bytes:1998889110 // observer转发给obproxy数据大小
	client_response_bytes:1998889110 // obproxy转发给client数据大小
cmd_time_stats=
	client_transaction_idle_time_us=0 // 在事务中该条SQL与上一条SQL执行结束之间的间隔时间, 即客户端事务中SQL间隔时间
	client_request_read_time_us=0 // obproxy读取客户端SQL耗时
	server_request_write_time_us=0 // obproxy将客户端SQL转发给observer耗时
	client_request_analyze_time_us=15 // obproxy 解析本条SQL消耗时间
	cluster_resource_create_time_us=0 // 如果执行该条SQL, 需要收集OB cluster相关信息(一般发生在第一次连接到该集群的时候)，建立集群相关信息耗时
	pl_lookup_time_us=3158 // 查询partition location耗时
	prepare_send_request_to_server_time_us=3196 // 从obproxy接受到客户端请求，到转发到observer执行前总计时间，正常应该是前面所有时间之和
	server_process_request_time_us=955 // observer处理请求的时间，对于流式请求就是从observer处理数据，到第一次转发数据的时间
	server_response_read_time_us=26067801 // observer转发数据到obproxy耗时，对于流式请求observer是一边处理请求，一边转发数据(包含网络上的等待时间)
	client_response_write_time_us=716825 // obproxy将数据写到客户端耗时
	request_total_time_us=26788969 // 处理该请求总时间
sql=select * from sbtest1 // 请求SQL
```
其中 `request_total_time_us` 记录了ODP 处理该请求的总耗时，`server_process_request_time_us` 记录了 ODP 向 OBServer 发出的请求的耗时，包括网络耗时和 OBServer 处理的耗时。通过该日志，我们可以定位慢 SQL 各阶段的耗时，从而更方便的定位慢 SQL 问题；


### 参考文档
1. [ODP 性能瓶颈](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000001050113)，；


