---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/OceanBase 悬挂事务与长事务/","dgPassFrontmatter":true}
---


### OceanBase 悬挂事务与长事务
#### 1 

适用版本：V2.1.x、V2.2.x、V3.1.x、V3.2.x  
悬挂事务是指客户端与 OBProxy 之间已经断开，但 OBProxy 与数据库端的链接仍然存在，由于事务超时时间比较长，连接上的长时间存在的事务；  
长事务是指链路连接正常，事务执行时间比较长的事务；  
任何活跃事务都会持有 MemTable 的 reference，如果事务长时间不结束，则会使 MemStore 冻结后无法释放，最终导致内存不足；  

```sql  
-- 查看所有 Server 信息，用于确定是否存在异常 OBServer 节点  
select * from __all_server;  
  
-- 查看当前长事务  
-- 创建后执行时间大于 10 分钟的事务  
SELECT * FROM __all_virtual_trans_stat WHERE (now() - ctx_create_time) > 600;  
SELECT * FROM __all_virtual_trans_stat  
WHERE is_exiting !=1 AND part_trans_action <= 2  
AND ctx_create_time < DATE_SUB(NOW(), INTERVAL 1200 SECOND)  
LIMIT 100;  
```  
  
可以通过配置 ob_query_timeout 与 ob_trx_timeout 配置项，避免事务长时间不结束，影响系统稳定性。有关以上两个配置项的详细信息与配置方式，请参见 [影响 OceanBase 数据库连接超时的相关配置](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000000127)，；  

### 参考文档：  
  
1. [悬挂事务与长事务](https://www.oceanbase.com/knowledge-base/oceanbase-database-20000001003?back=kb)，；  
2. [事务问题排查](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355019#10-title-%E4%BA%8B%E5%8A%A1%E4%B8%8D%E7%BB%93%E6%9D%9F%E9%97%AE%E9%A2%98)，；



