---
{"number headings":"auto, first-level 5, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/分析 observer 日志/","dgPassFrontmatter":true}
---


### 分析 observer 日志

##### 1 通过跳板机登录指定服务器及目录
```shell
# 1.通过跳板机登录指定服务器  
	ssh 100.64.132.29 ssh 100.64.132.30 ssh 100.64.132.94  
	ip addr    # 查询服务器 ip 地址

# 2.进入日志目录
	cd /home/admin/oceanbase/log        # OB Server 日志目录，rootservice 日志也在该目录
		# 若未使用默认目录，可以使用下面的命令查询实际目录  
		find /home -name "clog"
	cd /home/admin/logs/obproxy/log     # OB proxy 日志目录  
	ll |grep 2023070318  
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
```



### 参考文档



