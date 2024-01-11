---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/03_OceanBase 高阶进阶/查询 clog 日志大小/","dgPassFrontmatter":true}
---


### 查询 clog 日志大小

#### 0.1 通过跳板机连上 OBServer 服务器
```shell
ssh 100.64.174.228 # 通过跳板机连上 OBServer 服务器  
ip addr # 查询服务器 ip 地址  
```

#### 查询 Clog 日志大小
```shell
# OB Clog 目录一般在 /home/admin/oceanbase/store/zwy_hz_23011801，
ll /home/admin/oceanbase/store/zwy_hz_23011801 #  
	# 部分输出结果：lrwxrwxrwx 1 root root 31 Jan 18 2023 clog -> /data/log1/zwy_hz_23011801/clog  

# 若 Clog 不在该目录，则利用下面的命令查询实际目录  
	find / -type f name clog*  
	find /home -name "clog"  
		# 输出：/home/admin/oceanbase/store/zwy_hz_23011801/clog  
  
ll /home/admin/oceanbase/store/zwy_hz_23011801/  
	# zwy_hz_23011801 为集群名称
	# 输出：lrwxrwxrwx 1 root root 31 Jan 18 2023 clog -> /data/log1/zwy_hz_23011801/clog  
  
#  
cd /data/log1/zwy_hz_23011801/clog  
ll  
	# 若 Clog 全部当天的，则需要预估全天的 Clog 大小；  
	# 若存在多天的 Clog，则需要计算当天的 Clog 文件数 * 64MB，一般 Clog 默认大小为 67108864，即64MB；  
  
# 查询 Clog 整个文件夹大小  
cd ../  
du -sh *
```


### 参考文档



