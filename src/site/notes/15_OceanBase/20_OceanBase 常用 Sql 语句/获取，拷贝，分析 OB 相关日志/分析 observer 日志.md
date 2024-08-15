---
{"number headings":"auto, first-level 5, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/分析 observer 日志/","dgPassFrontmatter":true}
---


### 分析 observer 日志

##### 1 通过跳板机登录指定服务器及目录
```shell
# 1.通过跳板机登录指定服务器  
	ssh 100.64.132.29 ssh 100.64.132.30 ssh 100.64.132.94  
	ip addr        # 查询服务器 ip 地址
	hostname -i    # 

# 2.进入日志目录
	cd /home/admin/oceanbase/log        # OB Server 日志目录，rootservice 日志也在该目录
	cd /home/admin/logs/obproxy/log     # OB proxy 日志目录  
	ll |grep 2023070318  

	# 若未使用默认目录，可以使用下面的命令查询实际目录  
	find /home -name "clog"
```

#### 分析日志
##### 1 通过 trace_id 获取日志
```shell
# 1.前提条件：进入相应的日志目录

# 2.查询获取 trace_id
	## 通过 last_trace_id() 获取 trace_id
	select last_trace_id();   # YB426440AE66-0005F28319F16BB5-0-0

	## 通过 ob_sql_audit 获取 trace_id
	SELECT /*+ QUERY_TIMEOUT(10000000) READ_CONSISTENCY(WEAK) */ 
		svr_ip,svr_port,TRACE_ID,tenant_id,user_name, db_name, sql_id, plan_id, elapsed_time, execute_time, query_sql 
	FROM oceanbase.gv$ob_sql_audit 
	WHERE tenant_id = 1001 and user_name = 'xxxx' and query_sql like'%%'
	limit 10 ;

# 获取 trace_id 所对应的日志，执行 grep $trace_id observer.log 命令
grep "YB426440AE66-0005F28319F16BB5-0-0" observer.log
grep "YB426440AE66-0005F28319F16BB5-0-0" observer.log.20240814*
```


##### 2 直接分析日志
```shell
# 过滤文件日志  
ll | grep observer.log.20230905  
  
# 查询日志  
tail -n 100 observer.log.20230905000508  
  
cat observer.log.20230903130411 | grep "OSS"  
cat observer.log.20230903083423 | grep "ERROR"  
cat observer.log.20230903083423 | grep "ERROR" | grep "9003"  
  
grep "OSS" observer.log.20230905*  
grep "ERROR" observer.log.wf.20230905*  
grep "-9003" observer.log.wf.20230905*  
  
grep "ERROR" rootservice.log*  
grep "ERROR" election.log*  
grep "ERROR" obesi-daemon.log*

tail -f observer.log | grep "ERROR"      # 循环查询日志
```


### 参考文档



