---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/99_内部表介绍/oceanbase.__all_virtual_trans_stat/","dgPassFrontmatter":true}
---


### oceanbase.__all_virtual_trans_stat
#### 1 功能

`oceanbase.__all_virtual_trans_stat` 表展示的是整个集群中未结束事务的参与者上下文信息，包括主集群和备集群。对于用户来说，需要通过收集当前事务的所有参与者信息，从中构造出当前整个事务的执行状态；  

> [!NOTE] 修改记录：

#### 2 字段说明
`svr_ip`：表示创建该事务上下文的 OBServer 的 IP 地址；
`session_id`：表示该事务上下文所属会话的唯一 ID；
`proxy_id`：表示客户端 OBProxy/Java Client 对应的 IP 地址与端口号；
`trans_id`：表示该事务的唯一 ID；
`is_existing`：表示当前事务上下文是否正在退出；
`partition`：表示当前事务上下文在哪个分区上创建；  
`participants`：当前事务的参与者列表；
`ctx_create_time`：事务上下文的创建时间；  
`ref`：表示事务上下文当前的引用计数；
`sql_no`：表示当前事务上下文上最后一次执行的 SQL 的 sql_no；
`state`：表示事务上下文当前的状态。 可能的状态有 INIT、PREPARE、COMMIT、ABORT 与 CLEAR；
`part_trans_action`：表示该事务上下文上最后执行的动作。 可能的状态有 START_TASK、END_TASK、COMMIT；
`lock_for_read_entry_count`：表示该事务在表扫描过程中遇到锁冲突重试的次数；


### 参考文档
1. [事务问题排查](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355019)，；



