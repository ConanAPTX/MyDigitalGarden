---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/OceanBase 自适应合并/","dgPassFrontmatter":true}
---


### OceanBase 自适应合并
*OceanBase 数据库从 V4.2.0 版本开始支持自适应合并*；开启自适应合并后，系统会实时采集并统计用户的查询、写入等信息，再根据这些信息来识别可能导致的查询慢的场景，自适应地调度合并任务，达到对用户无感知地解决相关场景可能导致的慢查询问题。自适应合并开启期间，系统以分区为维度进行统计信息的采集及合并任务的调度；

> [!NOTE] 使用自适应合并功能，可以解决如下问题：
> 1. 避免一部分 Buffer 表问题的出现；
> 2. 优化导数场景下的查询性能；
> 3. 识别并规避一部分慢查询场景；

#### 1 触发规则

当分区 Leader 满足一定条件时，系统就会自动为该分区及 Follower 副本调度自适应合并。自适应合并的触发规则如下：
-   系统通过分析一段时间内分区的统计信息，获取到该分区查询和转储的次数以及增量数据中 Insert 行数的占比，确认该场景是否为导数场景。如果是导数场景，则对该分区触发自适应合并。
-   系统通过分析一段时间内分区的统计信息，获取到该分区转储的次数以及增量数据中更新和删除行的占比，确认该场景是否为导数场景。如果是导数场景，则对该分区触发自适应合并。
-   系统通过统计分区增量数据的总行数，确认该场景是否为持续写入场景，如果是持续写入场景，系统对该分区触发自适应合并。
-   系统通过查询扫描的数据量与实际有效数据的比例，确认针对该分区的查询是否为慢查询，如果为慢查询，系统对该分区触发自适应合并。
    

#### 2 注意事项
-   自适应合并可以有效减少一部分查询慢的问题，如果您的业务对查询性能要求比较高，并且存在自适应合并所支持的场景时，建议不要关闭自适应合并功能；
    
-   在 OceanBase 数据库中，名称以 "\_" 开头的配置项均为隐藏配置项，仅供开发人员在故障排查或紧急运维时使用。如果确认需要关闭自适应合并功能，请联系技术支持人员协助处理；
    

#### 3 开启或关闭自适应合并
自适应合并功能由租户级隐藏配置项 `_enable_adaptive_compaction` 进行控制，默认为开启状态；

由于自适应合并调度任务的执行会占用部分系统资源，如果不希望执行自适应合并操作，可以通过将租户级隐藏配置项 `_enable_adaptive_compaction` 的值修改为 `False` 来关闭自适应合并功能。关闭后，系统内部将不再进行自适应合并的调度；


```sql
-- 1. 使用 `root` 用户登录集群的 `sys` 租户

-- 2. 查询租户级隐藏配置项 `_enable_adaptive_compaction` 的值；
obclient [oceanbase]> SELECT * FROM oceanbase.GV$OB_PARAMETERS WHERE NAME LIKE '_enable_adaptive_compaction';
+----------------+----------+-------+--------+-----------+-----------------------------+-----------+-------+---------------------------------------------------------------------------------+---------+-------------------+---------------+-----------+
| SVR_IP         | SVR_PORT | ZONE  | SCOPE  | TENANT_ID | NAME                        | DATA_TYPE | VALUE | INFO                                                                            | SECTION | EDIT_LEVEL        | DEFAULT_VALUE | ISDEFAULT |
+----------------+----------+-------+--------+-----------+-----------------------------+-----------+-------+---------------------------------------------------------------------------------+---------+-------------------+---------------+-----------+
| 172.xx.xxx.xxx |     2882 | zone1 | TENANT |         1 | _enable_adaptive_compaction | BOOL      | True  | specifies whether allow adaptive compaction schedule and information collection | TENANT  | DYNAMIC_EFFECTIVE | True          | YES       |
| 172.xx.xxx.xxx |     2882 | zone1 | TENANT |      1001 | _enable_adaptive_compaction | BOOL      | True  | specifies whether allow adaptive compaction schedule and information collection | TENANT  | DYNAMIC_EFFECTIVE | True          | YES       |
| 172.xx.xxx.xxx |     2882 | zone1 | TENANT |      1002 | _enable_adaptive_compaction | BOOL      | True  | specifies whether allow adaptive compaction schedule and information collection | TENANT  | DYNAMIC_EFFECTIVE | True          | YES       |
| 172.xx.xxx.xxx |     2882 | zone1 | TENANT |      1003 | _enable_adaptive_compaction | BOOL      | True  | specifies whether allow adaptive compaction schedule and information collection | TENANT  | DYNAMIC_EFFECTIVE | True          | YES       |
| 172.xx.xxx.xxx |     2882 | zone1 | TENANT |      1004 | _enable_adaptive_compaction | BOOL      | True  | specifies whether allow adaptive compaction schedule and information collection | TENANT  | DYNAMIC_EFFECTIVE | True          | YES       |
+----------------+----------+-------+--------+-----------+-----------------------------+-----------+-------+---------------------------------------------------------------------------------+---------+-------------------+---------------+-----------+
5 rows in set

-- 3. 修改租户级隐藏配置项 `_enable_adaptive_compaction` 的值，关闭或开启自适应合并功能；
obclient [oceanbase]> ALTER SYSTEM SET _enable_adaptive_compaction = False TENANT = tenant_name;  -- 关闭
obclient [oceanbase]> ALTER SYSTEM SET _enable_adaptive_compaction = True TENANT = tenant_name;   -- 开启
```


### 参考文档

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/02-ocean-base/02//ocean-base/#" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



### 参考文档：
1. [存储架构概述](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000354478)，介绍 OB 存储架构，及存储引擎的功能：数据存储，转储合并，查询读写，数据校验；
	 1. [转储和合并](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355112)，转储和合并概述；
2. [管理数据存储](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355954)，包括转储管理，合并管理，数据压缩；
	1. [转储管理](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355954)，包括自动触发转储，自动触发转储，手动触发转储，查询转储信息，修改转储配置；
	2. [OceanBase 数据库的分层转储功能](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000000023?back=kb)，介绍 OceanBase 数据库的分层转储功能；
	3. [LSM-Tree 和 OceanBase 分层转储](https://my.oschina.net/actiontechoss/blog/8563823)，详细分层转储原理及相关参数介绍；
3. [如何修改 OceanBase 数据库的合并方式](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000001021?back=kb)，；
4. *合并管理概述*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819293)，；
5. *自动触发合并*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819289)，；
6. *定时触发合并*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819290)，；
7. *自适应合并*：[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819294)，；
8. *手动触发合并*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946263)，[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000886130)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819291)，；
9. *手动控制合并*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-0000000001417800)，[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-0000000001467935)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819292)，；
10. *查看合并信息*：[V3.2.4](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945932)，[V4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000886131)，[V4.3.1](https://www.oceanbase.com/docs/common-oceanbase-database-cn-1000000000819295)，；
	1. [如何查看 Compaction 的当前合并进度及历史记录](https://www.oceanbase.com/knowledge-base/oceanbase-database-1000000000209910?back=kb)，；
	2. [查询合并过程_v4.0.0](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000886131)，；
	3. [转储查看](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000376777)，；


</div></div>



