---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/OceanBase 事务分类/","dgPassFrontmatter":true}
---


### OceanBase 事务分类
#### 1 本地事务，分布式事务概述  
本地事务是相对于跨机分布式事务而言的，特指事务所操作的表的分区 Leader 全部在同一个 Server 上，并且与 Session 建立的 Server 具有相同的事务；根据操作的表的数量，本地事务可以继续细分为：`本地单分区事务`，`本地多分区事务`；详细情况：[本地事务](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355163)，；  
OceanBase 数据库的事务类型由事务 Session 位置和事务涉及的分区 Leader 数量两个维度来决定，主要分为：`分布式事务`，`单分区事务`；  

##### 1.1 本地单分区事务  
`本地单分区事务`，也是`单分区事务`：是最简单的模型，事务的提交采用了极高的优化；  

> [!NOTE] 本地单分区事务需要满足以下两个条件：  
> 1. 事务涉及的操作总共涉及一个分区；  
> 2. 分区的 Leader 与 Session 创建的 Server 相同；  
  
##### 1.2 本地多分区事务  
由于 OceanBase 数据库分区级日志流的设计，单机多分区事务本质上也是分布式事务。为了提高单机的性能，OceanBase 数据库对事务内参与者副本分布相同的事务做了比较多的优化，相对于传统两阶段提交，大大提高了单机事务提交的性能；  

> [!NOTE] 类似于本地单分区事务，本地多分区事务也需要满足两个条件：  
> 1. 事务涉及的表所涉及的多个分区， 其 Leader 在同一个 Server 上；  
> 2. 分区 Leader 与 Session 创建的 Server 相同；  
  
##### 1.3 分布式事务  
分布式事务满足事务的所有属性，同样需要满足 ACID 的特性。在多机数据修改，且要保证原子性的场景，分布式事务能够发挥重要作用；  
为了保证上述特性，通常采用两阶段提交协议；有关两阶段提交协议的详细介绍，参见 [两阶段提交](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000356647)，；  

> [!NOTE] `分布式事务`，以下两种场景的事务均称为分布式事务：  
> 1. 事务涉及的分区数量大于一个；  
> 2. 事务涉及的分区数量只有一个，且分区 leader 和事务 session 位置不在同一个 server；  
  
###### 1.3.1 两阶段提交  
OceanBase 数据库实现了原生的两阶段提交协议，保证分布式事务的原子性；  
两阶段提交协议中包含两种角色：`协调者`(Coordinator)，`参与者`(Participant)；`协调者`负责整个协议的推进，使得多个参与者最终达到一致的决议；`参与者`响应协调者的请求，根据协调者的请求完成 prepare 操作及 commit / abort 操作；  
  
###### 1.3.2 分布式事务提交流程  
经典两阶段提交的实现的响应时延：4 次日志延迟 + 2 次 RPC 延迟，OceanBase 数据库在实现两阶段提交时，进行了优化，优化后其响应时延：1 次日志延迟 + 2 次 RPC 延迟；  

> [!NOTE] OceanBase 数据库两阶段提交进行了如下优化：  
> 1. 增加了 pre commit 阶段；  
> 2. 修改了 commit ok 应答客户端的时间，pre commit 完成后即可应答客户端；  
> 3. 增加了 clear 阶段；  

![lQLPJwxyt7H4I7PNAjrNAoewyVOjEiWrwboFfofrrqA0AA_647_570.png](/img/user/15_OceanBase/03_OceanBase%20%E9%AB%98%E9%98%B6%E8%BF%9B%E9%98%B6/01_attachments/lQLPJwxyt7H4I7PNAjrNAoewyVOjEiWrwboFfofrrqA0AA_647_570.png)

1. `PREPARE 阶段：`生成 prepare version；  
	1. 协调者向所有的参与者发起 prepare request；  
	2. 参与者收到 prepare request 之后，决定是否可以提交，如果可以则持久化 prepare log 并且向协调者返回 prepare 成功，否则返回 prepare 失败；  
2. `pre commit 阶段：`推高本地 max commit version；  
	1. 协调者收到所有参与者的 prepare ok 之后，通过所有参与者的 prepare version 计算出 commit version，并向参与者发起 commit req；  
	2. 参与者收到 commit req 之后推高本地 max commit version，并且向协调者返回 pre commit ok；  
	3. 协调者收到所有参与者的 pre commit ok 之后，即可应答客户端，在应用程序的角度来看，表示事务已提交完成；  
3. `COMMIT 阶段：`  
	1. 参与者在向协调者返回 pre commit ok，继续提交 commit log，日志持久化完成之后给协调者回复 commit ok 消息；  
4. `clear 阶段：`  

### 参考文档：

