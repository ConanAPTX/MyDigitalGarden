---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/管理数据存储/OceanBase 手动触发，控制合并_V4.0.0 之前版本/","dgPassFrontmatter":true}
---


### OceanBase 手动触发，控制合并_V4.0.0 之前版本
#### 1 手动触发合并
合并也可以通过手动触发来完成；手动触发可以通过 SQL 语句发起合并，还可以通过 OCP 手动发起合并；

从 V4.0.0 之前版本仅支持【`集群级`】合并；从 V4.0.0 及之后版本开始支持【`租户级`】合并；

##### 1.1 操作步骤
V4.0.0 之前版本仅支持【`集群级`】合并；从 V4.0.0 及之后版本开始支持【`租户级`】合并；
```sql 
-- 1. 使用 `root` 用户登录集群的 `sys` 租户；

-- 发起集群合并(使用 root 用户登录到数据库的 sys 租户)
	-- 可以在 "root@sys" 用户下，通过以下命令发起手动合并（忽略当前 MemStore 的使用率）
	ALTER SYSTEM MAJOR FREEZE;   -- 发起集群合并
		-- V4.0.0 之前版本仅支持【`集群级`】合并；
```

#### 2 手动控制合并

通过 `MAJOR FREEZE` 命令触发合并后，默认情况下，系统会自动调度合并任务，按照您指定的合并策略进行合并操作，例如，您可以打开轮转合并开关，系统会自动调度按 Zone 轮转合并；
OceanBase 数据库还提供了手动控制合并的方法，让用户手动控制合并流程；

典型场景如下：
-   合并过程中，其中一个 Zone 出现错误，例如 Checksum 校验出错，为了防止错误扩散到其他 Zone，可以停止自动调度或暂停合并，待问题排查解决后，再恢复自动调度或者手动控制完成合并；
-   您希望手动控制合并流程，由自己决定合并时间及各个 Zone 的合并顺序等，可以采用手动控制合并方法；

##### 2.1 前提条件
手动控制合并前，请确认已通过 `MAJOR FREEZE` 命令触发合并，手动触发合并的详细操作，请参见 [手动触发合并](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355962)，；

##### 2.2 开启手动控制合并开关
配置项 *enable_manual_merge* 用于控制是否开启手动控制合并，默认值为 False ，表示关闭手动控制合并开关，该配置项的更多信息，请参见 [enable_manual_merge](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355721)，；

```sql
-- 开启手动控制合并开关(使用 root 用户登录到数据库的 sys 租户)
ALTER SYSTEM SET enable_manual_merge='True';
```

##### 2.3 开始，暂停合并
```sql
/*
语法：
	ALTER SYSTEM START MERGE ZONE [=] 'zone_name';     	-- 开始合并
	ALTER SYSTEM SUSPEND MERGE [ZONE [=] 'zone_name'];	-- 暂停合并
	ALTER SYSTEM RESUME MERGE [ZONE [=] 'zone_name'];   -- 恢复合并
参数：
	[=]：表示等号（=）为可选；
	zone_name：表示准备开始合并的 Zone，多个 Zone 之间用英文逗号（,）分隔；
*/
-- 开始合并
ALTER SYSTEM START MERGE ZONE = 'zone1';    -- 开始合并指定的 Zone；
ALTER SYSTEM START MERGE ZONE = 'zone1'，'zone2';    -- 开始合并指定的 Zone；

-- 暂停正在进行中的合并任务；【注意：该操作是高危操作】
ALTER SYSTEM SUSPEND MERGE;
ALTER SYSTEM SUSPEND MERGE ZONE = 'zone1';

-- 暂停合并后，也可以恢复合并
ALTER SYSTEM RESUME MERGE;
ALTER SYSTEM RESUME MERGE ZONE = 'zone1';

-- 清理合并的报错
ALTER SYSTEM clear merge error;
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



