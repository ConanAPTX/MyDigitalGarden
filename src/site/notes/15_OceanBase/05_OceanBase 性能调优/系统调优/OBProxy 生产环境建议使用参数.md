---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/05_OceanBase 性能调优/系统调优/OBProxy 生产环境建议使用参数/","dgPassFrontmatter":true}
---


### OBProxy 生产环境建议使用参数
#### 1 obproxy 版本: 1.8.11.2

```sql
-- OBProxy 生产环境建议使用参数，版本：1.8.11.2
-- 二次路由相关参数  
show proxyconfig like 'enable_reroute';  
alter proxyconfig set enable_reroute = true;  
  
show proxyconfig like 'enable_ob_protocol_v2';  
alter proxyconfig set enable_ob_protocol_v2 = true;  
  
-- 内存使用参数  
show proxyconfig like 'proxy_mem_limited';  
alter proxyconfig set proxy_mem_limited = '10G';  
  
show proxyconfig like 'enable_compression_protocol';  
alter proxyconfig set enable_compression_protocol = False;  
  
-- tcp相关参数
-- TCP 连接参数：client 端
show proxyconfig like 'client_sock_option_flag_out';  
alter proxyconfig set client_sock_option_flag_out = 3;  -- 启用 KEEPALIVE，不启用 TCP NO_DELAY
  
show proxyconfig like 'client_tcp%';  
alter proxyconfig set client_tcp_user_timeout = 0; -- 推荐配置，等待 TCP 层 ACK 确认消息的超时时长，0 表示不做超时处理
-- alter proxyconfig set client_tcp_keepcnt = 2;      -- 最多发送的 KEEPALIVE 探活包个数，2个
alter proxyconfig set client_tcp_keepcnt = 5;      -- 推荐的配置
alter proxyconfig set client_tcp_keepintvl = 5;    -- 两个 KEEPALIVE 探活包的发送时间间隔，5秒
alter proxyconfig set client_tcp_keepidle = 5;     -- 启动 KEEPALIVE 探活前的 idle 时间，5秒

-- TCP 连接参数：server 端
show proxyconfig like 'sock_option_flag_out';  
alter proxyconfig set sock_option_flag_out = 3;    -- 推荐的配置，启用 TCP NO_DELAY 和 KEEPALIVE

show proxyconfig like 'server_tcp%';  
alter proxyconfig set server_tcp_user_timeout = 0; -- 推荐配置，等待 TCP 层 ACK 确认消息的超时时长，0 表示不做超时处理
-- alter proxyconfig set server_tcp_keepcnt = 2;      -- 最多发送的 KEEPALIVE 探活包个数，2个
alter proxyconfig set server_tcp_keepcnt = 5;      -- 推荐配置
alter proxyconfig set server_tcp_keepintvl = 5;    -- 两个 KEEPALIVE 探活包的发送时间间隔，5秒
alter proxyconfig set server_tcp_keepidle = 5;     -- 启动 KEEPALIVE 探活前的 idle 时间，5秒
  
show proxyconfig like 'log_dir_size_threshold';  
alter proxyconfig set log_dir_size_threshold = '50G';
```


### 参考文档

