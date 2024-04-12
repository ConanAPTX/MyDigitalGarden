---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/02_OceanBase 基本操作/集群和多租户管理/通用查找 SQL 请求日志/","dgPassFrontmatter":true}
---


### 通用查找 SQL 请求日志

OceanBase 数据库支持获取目标 SQL 请求的完整日志；  
为了便于查找目标 SQL 请求日志，您可提前通过设置日志打印级别来减少 SQL 模块之外的日志的打印。有关日志打印级别的相关设置请参见 [设置日志打印级别](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000946342)，；  

#### 1 具体操作步骤如下：  
```shell  
# 1.登录 OBServer 所在的服务器  
  
# 2.进入日志文件所在的目录  
cd ~/oceanbase/log  
  
# 3.根据设置的日志级别，筛选出该级别的日志。例如：查看 WARN 级别的 observer.log 日志  
grep 'WARN' observer.log  
  
# 4.然后再根据筛选出的日志的 trace_id 获取某条 SQL 请求的完整日志  
grep $trace_id observer.log  
# YB42AC1E87ED-0005C6866C3BAFB1-0-0  
  
grep YB42AC1E87ED-0005C6866C3BAFB1-0-0 observer.log  
```  


### 参考文档



