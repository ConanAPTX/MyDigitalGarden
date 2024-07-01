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


#### 2 获取 OMS 日志
##### 2.1 获取 store 组件日志
```shell
# 1.进入 docker 容器中
	hostname -i
	docker ps # 查询所有正在运行的容器

	## 进入容器 docker exec -it 容器ID|容器名称 /bin/bash 
	docker exec -it 78f45ee7e496 /bin/bash ## 100.63.40.216 
	docker exec -it 9a58151ef7bd /bin/bash ## 100.63.41.216

	## /home/ds/store/store{storeport}/log  
		# storeport：组件 ID 中的端口；组件 ID 可以通过 OMS 白屏界面获取；
	cd /home/ds/store/store7131/log  
		# 100.63.40.216-7131:ob_ten_1_5ukd9qzmhk3k-1-0:0001000066  

# 2.退出 docker 执行
	## 从 docker 中拷贝文件到本机器
	docker cp 78f45ee7e496:/home/ds/store/store7131/log/ /tmp/conan/log_store7131  
		# 拷贝文件夹
	
	## 压缩文件
	cd /tmp/conan/  
	tar -zcvf ./log_store7131.tar.gz ./log_store7131/   # 压缩
	# tar -zxvf ./log_store7131.tar.gz                    # 解压
```

##### 2.2 获取 coonector 组件日志
```shell
# 1.进入 docker 容器中
	hostname -i
	docker ps # 查询所有正在运行的容器

	## 进入容器 docker exec -it 容器ID|容器名称 /bin/bash 
	docker exec -it 78f45ee7e496 /bin/bash ## 100.63.40.216 
	docker exec -it 9a58151ef7bd /bin/bash ## 100.63.41.216
	
	# cd /home/ds/run/{name}/logs 
		# name：组件 ID；组件 ID 可以通过 OMS 白屏界面获取；
	cd /home/ds/run/100.63.40.216-9000:connector:sync_5urtwvy7l2w0_1_0:0000000007/logs  


# 2.退出 docker 执行
	## 从 docker 中拷贝文件到本机器
	# docker cp 容器名称:/home/admin/oceanbase/log/observer.log /tmp/observer.log_20231130  
	docker cp 78f45ee7e496:/home/ds/run/100.63.40.216-9000:connector:sync_5urtwvy7l2w0_1_0:0000000007/logs/plugin-error.log /tmp/conan/plugin-error.log
		# 拷贝单个文件；  
	docker cp 78f45ee7e496:/home/ds/run/100.63.40.216-9000:connector:sync_5urtwvy7l2w0_1_0:0000000007/logs/ /tmp/conan/  
		# 拷贝文件夹

	# 压缩文件
	cd /tmp/conan/  
	tar -zcvf ./logs.tar.gz ./logs/  
		# 100.63.40.216-9000:connector:sync_5urtwvy7l2w0_1_0:0000000007  

```


#### 3 拷贝日志文件到跳板机
```shell
# 在跳板机上创建文件夹，若存在则不需要创建；
sudo mkdir /tmp/conan/  
cd /tmp/conan/ 

hostname -i
	# 跳板机 ip，；
	
# 拷贝文件到跳板机【跳板机上执行】
	scp 100.63.40.216:/tmp/conan/log_store7131.tar.gz /tmp/conan/   
	scp 100.63.40.216:/tmp/conan/logs.tar.gz /tmp/conan/
```


### 参考文档
1. [[15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/拷贝 Oceanbase 日志文件_2\|拷贝 Oceanbase 日志文件_2]]，；
2. [[15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OMS/OMS 全量数据迁移报错\|OMS 全量数据迁移报错]]，；
