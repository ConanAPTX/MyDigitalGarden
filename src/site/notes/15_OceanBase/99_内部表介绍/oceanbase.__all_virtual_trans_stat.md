---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/99_内部表介绍/oceanbase.__all_virtual_trans_stat/","dgPassFrontmatter":true}
---


### oceanbase.\_\_all_virtual_trans_stat
#### 1 功能

展示正在进行的事务状态信息；
该表展示的是整个集群中未结束事务的参与者上下文信息，包括主集群和备集群。对于用户来说，需要通过收集当前事务的所有参与者信息，从中构造出当前整个事务的执行状态；  

> [!NOTE] 修改记录：

#### 2 相关视图


#### 3 常用查询

`__all_virtual_trans_stat` 虚拟表统计的是集群内部所有活跃事务的信息。事务涉及 N 个 Partition 的写操作，每个 Partition 产生一条记录。通过该虚拟表可统计耗时长的事务；

```sql
SELECT svr_ip, trans_id, `partition` from __all_virtual_trans_stat 
where tenant_id = xxx and sql_no > 0 and ctx_create_time < date_sub(now(), INTERVAL 100 SECOND);
	-- sql_no：若值 > 0 表示 Leader 上的上下文信息，若值为 0 表示备机回放出来的上下文；因此对于长事务的诊断，需要带上 `sql_no > 0` 的约束条件；
```



#### 4 字段说明
1. `tenant_id`：bigint(20) ，NO，租户 ID；
2. `svr_ip`：表示创建该事务上下文的 OBServer 的 IP 地址；
3. `svr_port`：bigint(20)，NO，端口；
4. `session_id`：表示该事务上下文所属会话的唯一 ID；
5. `proxy_id`：表示客户端 OBProxy/Java Client 对应的 IP 地址与端口号；
6. `trans_type`：bigint(20)，NO，事务类型；
7. `trans_id`：表示该事务的唯一 ID；
8. `is_existing`：表示当前事务上下文是否正在退出；
9. `is_readonly`：bigint(20)，NO，是否只读；
10. `active_memstore_version`：varchar(64)，NO，活跃 memstore 的版本；
11. `partition`：表示当前事务上下文在哪个分区上创建；  
12. `participants`：当前事务的参与者列表；
13. `autocommit`：bigint(20)，NO，是否自动提交；
14. `trans_consistency`：bigint(20)，NO，事务一致性级别；
15. `ctx_create_time`：事务上下文的创建时间；  
16. `expired_time`：timestamp(6)，YES，过期时间；
17. `refer`：表示事务上下文当前的引用计数；
18. `sql_no`：表示当前事务上下文上最后一次执行的 SQL 的 sql_no；
19. `state`：表示事务上下文当前的状态。 可能的状态有 INIT、PREPARE、COMMIT、ABORT 与 CLEAR；
20. `part_trans_action`：表示该事务上下文上最后执行的动作。 可能的状态有 START_TASK、END_TASK、COMMIT；
21. `lock_for_read_entry_count`：表示该事务在表扫描过程中遇到锁冲突重试的次数；


### 参考文档
1. [事务问题排查](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355019)，；
2. [\_\_all_virtual_trans_stat_V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945509)，；



