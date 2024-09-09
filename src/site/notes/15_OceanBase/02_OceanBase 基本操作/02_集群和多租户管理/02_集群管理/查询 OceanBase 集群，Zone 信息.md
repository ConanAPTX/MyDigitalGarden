---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/02_集群和多租户管理/02_集群管理/查询 OceanBase 集群，Zone 信息/","dgPassFrontmatter":true}
---


### 查询 OceanBase 集群，Zone 信息
#### 1 查看集群信息

查看集群 id、集群名字和创建时间等信息  
```sql  
-- 查看集群 id、集群名字和创建时间等信息  
obclient -h10.10.10.1 -P2881 -uroot@sys -p -A -- 使用 root 用户登录数据库的 sys 租户 

show parameters like 'cluster';    -- 查询集群名

SELECT * FROM oceanbase.v$ob_cluster\G;   -- 查询集群信息
```  
更多 `v$ob_cluster` 视图的字段及说明信息，请参见 [v$ob_cluster](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000945272)，； 
  
#### 2 查看集群中所有的 Zone 信息  
```sql  
-- 查看集群中所有的 Zone 信息  
obclient -h10.10.10.1 -P2881 -uroot@sys -p -A     -- 使用 root 用户登录数据库的 sys 租户  
SELECT * FROM oceanbase.__all_zone WHERE name = 'idc';
SELECT * FROM oceanbase.__all_zone WHERE name = 'region';
/* 字段介绍：
	gmt_create：创建时间；  
	gmt_modified：更新时间；  
	zone：zone 名称；  
	name：信息名称；  
		该隔离 Zone 的 name 列中 status 字段为 INACTIVE，表示该 Zone 处于 INACTIVE 状态；  
	value：信息值；  
	info：信息值字符串表示；  
	is_merge_error：；
	status 是否全为ACTIVE
*/  
```  

从 V4.0.0 版本开始引入 *DBA_OB_ZONES* 视图，该视图展示所有 Zone 的信息； 
```sql
SELECT * FROM oceanbase.DBA_OB_ZONES WHERE name = 'idc';
```
关于该视图更多信息；[[15_OceanBase/99_内部表介绍/字典视图_数据库基本信息_01#1 DBA_OB_ZONES\|字典视图_数据库基本信息_01#1 DBA_OB_ZONES]]，；


### 参考文档



