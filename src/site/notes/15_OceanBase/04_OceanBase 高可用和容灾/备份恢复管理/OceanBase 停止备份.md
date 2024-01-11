---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 高可用和容灾/备份恢复管理/OceanBase 停止备份/","dgPassFrontmatter":true}
---


### OceanBase 停止备份
OceanBase 备份 包括【*数据备份*】和【*日志备份*】；
  
#### 1 通过 SQL 语句停止备份

> [!NOTE] 说明：
> 1. 停止`数据备份`任务后，如果再次发起数据备份，系统会重新开始备份；

```sql
-- 停止数据备份任务(使用 root 用户登录数据库的 sys 租户)
obclient> ALTER SYSTEM CANCEL BACKUP;
	-- 停止数据备份任务后，如果再次发起数据备份，系统会重新开始备份；

-- 停止日志备份任务(使用 root 用户登录数据库的 sys 租户)
obclient> ALTER SYSTEM NOARCHIVELOG;
```

#### 2 通过 OCP 停止备份
详细情况：[通过 OCP 停止备份](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355897)，；

### 参考文档
1. [通过 OCP 停止备份](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000355897)，；









