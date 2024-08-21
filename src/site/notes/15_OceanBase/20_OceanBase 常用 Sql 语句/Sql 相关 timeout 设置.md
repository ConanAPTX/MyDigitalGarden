---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/20_OceanBase 常用 Sql 语句/Sql 相关 timeout 设置/","dgPassFrontmatter":true}
---



### Sql 相关 timeout 设置
#### 1 Sql 相关 timeout 设置

1. `ob_query_timeout` / `ob_trx_timeout` / `ob_trx_idle_timeout`：

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">



2. `ob_query_timeout`：用于设置 SQL 最大执行时间，单位是微秒；【推荐使用默认值】 

</div></div>


<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">



8. ob_trx_timeout / ob_trx_idle_timeout： 

</div></div>


```sql
SHOW VARIABLES LIKE 'ob_query_timeout';  
SHOW VARIABLES LIKE 'ob_trx_timeout';  
SHOW VARIABLES LIKE 'ob_trx_idle_timeout';  
  
BEGIN; -- 开启事务  
SET SESSION ob_query_timeout = 10000000000;     -- 查询超时：用于设置 SQL 最大执行时间，单位为微秒；  
SET SESSION ob_trx_timeout = 100000000000;      -- 用于设置事务超时（未提交）时间，单位为微秒；  
SET SESSION ob_trx_idle_timeout = 120000000000; -- 用于设置事务空闲超时时间，即事务中两条语句之间的执行间隔超过该值时超时，单位为微秒；  
ckd_birth_tb_eh_hz;  
END; -- 提交事务  
ROLLBACK; -- 回滚事务
```
  





### 参考文档



