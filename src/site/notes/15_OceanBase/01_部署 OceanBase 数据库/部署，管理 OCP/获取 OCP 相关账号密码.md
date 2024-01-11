---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OCP/获取 OCP 相关账号密码/","dgPassFrontmatter":true}
---


### 获取 OCP 相关账号密码
#### 1 

```shell
cd /root/t-oceanbase-antman/tools # 进入目录  
ll 
<<xxxx 输出结果：  
	drwxr-xr-x 2 root root 59 Aug 29 2022 deploy_docker  
	drwxr-xr-x 3 root root 290 Aug 29 2022 deploy_physical  
	-rwxr-x--- 1 root root 425 May 11 2022 getpass.sh  
	drwxr-xr-x 2 root root 158 Aug 29 2022 ob-checker  
	drwxr-xr-x 2 root root 64 Aug 29 2022 obctl  
	drwxr-xr-x 6 root root 136 Aug 29 2022 oceanbase_dba_helper  
	-rwxr-x--- 1 root root 2030 May 11 2022 setpass.sh  
	-rwxr-x--- 1 root root 1691 May 11 2022 update_meta_VIP.sh  	
xxxx

sh getpass.sh # 执行 getpass.sh 脚本  
<<xxxx 输出结果：  
	sys: Ez6IGJEO^n  
	meta: WD4Swl%3qE  
	monitor: a+hfhIl2YL  
	sysmonitor: SEJQ@n0fmC  
	omsmeta: T%CedWqK63  
	odcmeta: +NW1OV6JHx  
	proxysys: m%sw2C4{1w  
	proxyro: j0c8W600G%
xxxx
```



### 参考文档



