---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OMS/OMS 全量数据迁移报错/","dgPassFrontmatter":true}
---

 
### OMS 全量数据迁移报错


#### 1 适用版本
OMS 3.2.2 版本；

#### 2 问题现象
OMS 全量数据迁移报错， 错误信息：ORA-24816: Expanded non LONG bind data supplied after actual LONG or LOB column；

#### 3 可能原因
该故障为已知的版本 BUG，可以用以下方法解决：
1. 跳过报错，后期做全量数据校验是进行补数据：；
2. 使用补丁安装包进行热更新：对OMS影响较小，但有可能不能解决问题；【以下解决方式为该方法】；
3. 直接升级 OMS 版本：该方法对现有 OMS 影响较大，可以完全解决问题；


#### 4 解决方式
操作步骤：

```shell
# 1.下载指定的 rpm 包，并将补丁安装包上传到服务器上；
	# 1. 该安装包需要产研提供；
	
	# 1.高院连接方式
		ssh ob_query@100.63.41.87 # 密码：OceanBase##123  
		sudo -i # 切换 root 用户  
		  
		cd /opt  
		rz # 上传文件  
	
	# 连接目标服务器 
	ssh 100.63.40.216 , ssh 100.63.41.216  
	# sudo mkdir /tmp/conan/ ## 创建目录(根据实际情况是否执行)  
	  
	# 拷贝安装包到目标服务器  
		# 从远程复制到本地服务器  
		# scp 100.63.41.87:/root/ant-jdbc-connector-2.0.3-20220225180702.noarch /opt # 拷贝文件到其他机器  
			# 需要输入密码  
		  
		# 从本地远程复制到另一台服务器  
		scp /root/ant-jdbc-connector-2.0.3-20220225180702.noarch.rpm 100.63.40.216:/opt  
		scp /root/ant-jdbc-connector-2.0.3-20220225180702.noarch.rpm 100.63.41.216:/opt  
			# 在 100.63.41.87 机器上执行  
```

```shell 
# 2. 将 rpm 包传入 docker 容器中  
	docker ps # 查询所有正在运行的容器  
	  
	## 从执行命令所在的机器复制文件或目录到容器内部的指定路径  
	# 语法：docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH  
	cd /opt  
	docker cp /opt/ant-jdbc-connector-2.0.3-20220225180702.noarch.rpm oms_322bp2:/opt ## 100.63.40.216  
	docker cp /opt/ant-jdbc-connector-2.0.3-20220225180702.noarch.rpm oms_322bp2:/opt ## 100.63.41.216  
	  
	## 验证文件是否成功拷贝  
	# 语法：docker exec -it <容器名称> ls <目标容器路径>  
	docker exec -it oms_322bp2 ls /opt/  
  
# 3. 进入 docker 容器中  
	## 进入容器  
	docker exec -it 容器ID|容器名称 /bin/bash  
	docker exec -it 78f45ee7e496 /bin/bash ## 100.63.40.216  
	docker exec -it 9a58151ef7bd /bin/bash ## 100.63.41.216  
	  
	supervisorctl status # 获取输出信息，确认各个服务均为 RUNNING 状态。  
	# RUNNING 状态说明各个服务状态正常，OMS 已在环境中正常运行  
  
# 4. 进入rpm包传入的目录，ls 命令查看 rpm 包名。  
# 5. 若存在老的 jdbc-connector rpm 在更新到最新的 oboms-connector 需要删除之前老的 jdbc-connector  
	# 查询是否已安装  
	rpm -qa | grep connector # 会展示老的 ant-jdbc-connector-3.0.20-20220820014444.noarch 则需要先删除老包再热更  
	  
	rpm -qa | grep ant-jdbc-connector  
	查询结果：  
	ant-jdbc-connector-2.0.3-20220225180702.noarch ## 100.63.40.216  
	ant-jdbc-connector-2.0.3-20220225180702.noarch ## 100.63.41.216  
	  
	# 删除老包  
	# rpm -e ant-jdbc-connector-3.0.20-20220820014444.noarch  
	rpm -e ant-jdbc-connector-2.0.3-20220225180702.noarch ## 100.63.40.216  
  
# 6. 输入以下命令完成 rpm 包升级 rpm -Uvh 包名  
	cd /opt/  
	ll  
	rpm -Uvh ant-jdbc-connector-2.0.3-20220225180702.noarch.rpm  
  
# 7. 重启链路
```


### 参考文档


