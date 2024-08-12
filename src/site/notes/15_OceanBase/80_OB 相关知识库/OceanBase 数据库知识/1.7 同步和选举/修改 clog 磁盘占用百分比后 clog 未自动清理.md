---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/80_OB 相关知识库/OceanBase 数据库知识/1.7 同步和选举/修改 clog 磁盘占用百分比后 clog 未自动清理/","dgPassFrontmatter":true}
---


### 修改 clog 磁盘占用百分比后 clog 未自动清理
#### 1 适用版本
OceanBase 数据库 V2.x、V3.x 版本；

#### 2 问题现象
集群状态不可用，查看日志发现时 clog 磁盘占用空间超过 95%，手动停止 OBServer 进程后，在 admin 用户下执行以下语句。
```shell
cd  /home/admin/oceanbase && ./bin/observer -o "clog_disk_usage_limit_percentage=97"
```


集群正常启动后，将 `clog_disk_usage_limit_percentage` 改为 95 后，集群无法访问；

查看日志发现如下信息：
```shell
[2023-02-14 12:25:19.268085] ERROR [CLOG] update_free_quota (ob_log_file_pool.cpp:413) [89143][3714][Y0-0000000000000000] [lt=539] [dc=0] clog disk is almost full(type_=1, total_size=1081659957248, free_quota=-171216994304, warn_percent(%)=80, limit_percent=95, used_percent(%)=95) BACKTRACE:0xd20040b 0xd1e899f 0xd1e899f 0x8cc6b5b 0x7655ed3 0x765676f 0x7656b43 0x7656d9f 0x765711b 0x762315f 0x779797f 0x77243bf 0x7724423 0x772c12f 0x5ce2533 0x396155b 0xcfa192f 0xcf9dc63 0xcf9c557 0xcf9c557

[2023-02-14 12:25:17.234178] WARN  [STORAGE.TRANS] start_trans (ob_trans_service.cpp:410) [70036][518][YB428650D177-0005F49F4344FF72] [lt=21] [dc=0] clog disk size is not enough
```


#### 3 问题原因
业务中存在长事务，导致一直占用磁盘空间无法释放；


#### 4 解决方法
查询是否存在长事务，杀掉长事务后日志会自动会滚，磁盘占用空间陆续减少； 


### 参考文档
1. [修改 clog 磁盘占用百分比后 clog 未自动清理](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000267139?back=kb)，；


