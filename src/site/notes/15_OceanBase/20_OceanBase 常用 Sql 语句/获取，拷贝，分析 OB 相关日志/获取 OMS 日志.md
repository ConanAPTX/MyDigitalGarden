---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/获取 OMS 日志/","dgPassFrontmatter":true}
---


### 获取 OMS 日志

#### 1 连接 OMS 服务器
```shell
# OMS 机器：100.63.40.216 100.63.41.216  

# 通过黑屏方式登录 OMS 机器

	## 最后连接需要的服务器  
	ssh 100.63.40.216 
	ssh 100.63.41.216 

hostname -i    # 查看所在机器 IP；
```


#### 2 查询 OMS 日志路径
```shell
# 1.进入 docker 容器中
	hostname -i
	docker ps # 查询所有正在运行的容器

	## 进入容器 docker exec -it 容器ID|容器名称 /bin/bash 
	docker exec -it 78f45ee7e496 /bin/bash ## 100.63.40.216 
	docker exec -it 9a58151ef7bd /bin/bash ## 100.63.41.216

# 获取 OMS 日志文件路径
```


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/06-ocean-base/oms/oms/#3-1-store" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



##### 3.1 store 组件日志目录
Store 组件日志的默认目录为 `/home/ds/store/store{storeport}/log`；

```shell
## 获取 store 组件日志(增量拉取组件)
	## /home/ds/store/store{storeport}/log  
		# storeport：组件 ID 中的端口；组件 ID 可以通过 OMS 白屏界面获取；
	cd /home/ds/store/store7131/log  
		# 100.63.40.216-7131:ob_ten_1_5ukd9qzmhk3k-1-0:0001000066
```


</div></div>



<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/06-ocean-base/oms/oms/#4-1-full-import-incr-sync" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



##### 4.1 Full-Import / Incr-Sync 组件日志目录 
OMS 的 Full-Import(全量导入组件) / Incr-Sync(增量导入组件) 组件日志的默认目录为 `/home/ds/run/{name}/logs`；

```shell
## 获取 Full-Import(全量导入组件) / Incr-Sync(增量导入组件) 组件日志
	# cd /home/ds/run/{name}/logs 
		# name：组件 ID；组件 ID 可以通过 OMS 白屏界面获取；
	cd /home/ds/run/100.63.40.216-9000:connector:sync_5urtwvy7l2w0_1_0:0000000007/logs  
		# 组件 ID：100.63.40.216-9000:connector:sync_5urtwvy7l2w0_1_0:0000000007
```



</div></div>



<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/06-ocean-base/oms/oms/#5-1-full-verification" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



##### 5.1 Full-Verification 组件日志目录 
Full-Verification(全量校验组件) 组件日志的目录为 `/home/ds/run/{name}/logs`；

```shell
##  获取 Full-Verification(全量校验组件) 组件日志
	# cd /home/ds/run/{name}/logs
		# name：组件 ID；组件 ID 可以通过 OMS 白屏界面获取；
	cd /home/ds/run/192.10.111.111:90218:0000000004/logs
```




</div></div>




#### 3 从 docker 中拷贝文件到本机器
```shell
# 3.退出 docker 执行
	## 创建文件夹  
		# sudo mkdir /tmp/conan/20240807/  
		sudo mkdir -p /tmp/conan/20240807/    # 循环创建文件夹
		cd /tmp/conan/  

	## 从 docker 中拷贝文件到本机器
		# docker cp 容器名称:/home/admin/oceanbase/log/observer.log /tmp/observer.log_20231130
		docker cp 78f45ee7e496:/home/ds/store/store7131/log/ /tmp/conan/log_store7131    # 拷贝文件夹
		docker cp 78f45ee7e496:/home/ds/run/100.63.40.216-9000:connector:sync_5urtwvy7l2w0_1_0:0000000007/logs/plugin-error.log /tmp/conan/plugin-error.log
			# 拷贝单个文件；  
		docker cp 78f45ee7e496:/home/ds/run/100.63.40.216-9000:connector:sync_5urtwvy7l2w0_1_0:0000000007/logs/ /tmp/conan/   # 拷贝文件夹
		docker cp 78f45ee7e496:/home/admin/logs/ghana/Ghana/common-default.log /tmp/conan/20240807/common-default.log  
		docker cp 78f45ee7e496:/home/admin/logs/ghana/Ghana/common-error.log /tmp/conan/20240807/common-error.log  

	## 压缩文件
		cd /tmp/conan/  
		tar -zcvf ./log_store7131.tar.gz ./log_store7131/   # 压缩
		# tar -zxvf ./log_store7131.tar.gz                  # 解压
		tar -zcvf ./logs.tar.gz ./logs/  
		tar -zcvf ./20240807_log.tar.gz ./20240807/  
```


#### 4 拷贝日志文件到跳板机
```shell
# 3 在跳板机上创建文件夹，若存在则不需要创建；
# sudo mkdir /tmp/conan/20240614/
sudo mkdir -p /tmp/conan/20240614/     # 循环创建文件夹
cd /tmp/conan/20240614/ 
cd /apsarapangu/disk8/conan            # 南中心

hostname -i
	# 跳板机 ip，；
	
# 拷贝文件到跳板机【跳板机上执行】
	scp 100.63.40.216:/tmp/conan/log_store7131.tar.gz /tmp/conan/   
	scp 100.63.40.216:/tmp/conan/logs.tar.gz /tmp/conan/
	scp 100.63.40.216:/tmp/conan/20240807_log.tar.gz /tmp/conan/
```


#### 5 压缩文档并下载
```shell
# 4 压缩文档
cd /tmp/conan/  
tar -zcvf ./20240614.tar.gz ./20240614/  

# 将文件从跳板机拷贝到本地（通过 SecureCRT 连上跳板服务器）
cd /tmp/conan/  
ls | grep observer.log.wf.20230703185604 
sz ./20240614.tar.gz  
	# 下载到本地的文件默认保存目录：C:\Users\szy\Downloads
```

### 参考文档
1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/拷贝 Oceanbase 日志文件_2\|拷贝 Oceanbase 日志文件_2]]，；
2. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OMS/OMS 全量数据迁移报错\|OMS 全量数据迁移报错]]，；

```
	## 获取 store 组件日志
		## /home/ds/store/store{storeport}/log  
			# storeport：组件 ID 中的端口；组件 ID 可以通过 OMS 白屏界面获取；
		cd /home/ds/store/store7131/log  
			# 100.63.40.216-7131:ob_ten_1_5ukd9qzmhk3k-1-0:0001000066
			
	## 获取 coonector 组件日志
		# cd /home/ds/run/{name}/logs 
			# name：组件 ID；组件 ID 可以通过 OMS 白屏界面获取；
		cd /home/ds/run/100.63.40.216-9000:connector:sync_5urtwvy7l2w0_1_0:0000000007/logs  

	##  获取 common-default.log common-error.log 组件日志
		cd /home/admin/logs/ghana/Ghana/  
		ls | grep common  
			# 文件列表：common-default.log common-error.log
```
