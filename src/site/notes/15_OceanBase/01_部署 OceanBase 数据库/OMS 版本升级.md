---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/OMS 版本升级/","dgPassFrontmatter":true}
---


### OMS 版本升级_从 V3.2.2 版本升级至 V4.2.2

#### 1 背景信息

> [!NOTE] OMS V4.2.2 的版本升级包含以下两种场景：
> 1. 当前版本为 V3.2.1 及之后，V4.0.1 之前版本；
> 	1. 注意：如果是 OMS V3.2.1 之前的版本，您需要先升级至 V3.2.1；
> 2. 当前版本为 V4.0.1 及之后版本；

> [!NOTE] 从 OMS V3.2.1 及之后，V4.0.1 之前版本升级至 V4.2.2 时，需要比 V4.0.1 及之后版本升级至 V4.2.2 多执行以下两个操作：
> 1. 需要进行下文《前提条件》模块内容的确认；
> 2. 在升级过程中，需要执行升级 JAR；

> [!NOTE] `注意`：
> 1. 多节点升级时，您需要升级所有节点。请勿出现部分节点升级，部分节点未升级的情况；
> 2. OMS 在 V4.0.1 完成迁移同步架构融合操作，涉及相关表结构重构。所以若原 OMS 版本低于 V4.0.1 并且需要升级至 V4.0.1 及之后的版本，需要执行此操作；*非该场景，请勿执行此操作*；



#### 2 原 OMS 系统部署情况

```shell
部署模式：单地域多节点部署；
部署节点：100.63.40.216；100.63.41.216；

```


#### 3 前提条件  
##### 3.1 检查是否存在重名的数据迁移和数据同步项目
升级前，您需要检查是否存在重名的数据迁移和数据同步项目，如果存在，请对重名的项目名称进行修改，确保不要出现两个或多个项目的名称相同； 
  
```sql
-- 检查是否存在重名项目，若存在重名项目，请根据实际情况依次修改项目名称；
-- 1.数据迁移项目  
	-- 
	SELECT project_name,count(*) AS count,group_concat(id) AS ids FROM oms_project 
	WHERE project_status != "DELETED" GROUP BY project_name HAVING count(*) > 1;  

	--
	UPDATE oms_project SET project_name = <数据迁移项目的新名称> WHERE id = <数据迁移项目 ID>;  
  
-- 2.数据同步项目  
	-- 
	SELECT project_name,count(*) AS count,group_concat(id) AS ids FROM oms_sync_project 
	WHERE project_status != "DELETED" GROUP BY project_name HAVING count(*) > 1;  

	-- 
	UPDATE oms_sync_project SET project_name = <数据同步项目的新名称> WHERE id = <数据同步项目 ID>;  
```

##### 3.2 检查使用场景中存在级联场景
如果您的使用场景中存在级联场景（即某个 OceanBase 数据源，作为 A 项目的目标端的同时，也是 B 项目的源端），并且您曾更新过 Writer 的 blackRegionNo 参数，则您需要进行以下检查：  
  
```shell
# 在 OMS 容器内部，执行下述命令，获取 cm_location 对应的数字 xxx。  
  cat /home/admin/conf/config.yaml | grep 'cm_location'  

# 登录 OMS 的 drc_cm 数据库，执行下述命令。  
SELECT * FROM config_job WHERE `key`='sourceFile.blackRegionNo' AND VALUE!=xxx;  
	# 如果查询结果非空，且该级联项目仍在使用，请联系 OMS 值班同学处理。否则请继续升级操作。 
```

##### 3.3 拷贝安装包到目标服务器

```shell
# 通过跳板机连上 100.63.41.87，并上传安装包；
ssh ob_query@100.63.41.87 # 密码：OceanBase##123 
sudo -i # 切换 root 用户
cd /opt  
rz      # 上传文件  

# 拷贝安装包到目标服务器
	## 从本地远程复制到另一台服务器 
	scp /root/ant-jdbc-connector-2.0.3-20220225180702.noarch.rpm 100.63.40.216:/opt 
	scp /root/ant-jdbc-connector-2.0.3-20220225180702.noarch.rpm 100.63.41.216:/opt 
		# 在 100.63.41.87 机器上执行
```


#### 4 升级 OMS 操作步骤  
##### 4.1 如果开启了 HA 功能，请先关闭 HA； 
在 OMS 系统中修改 ha.config 参数的值，将其值设置 enable 为 false，即关闭 HA 功能；

##### 4.2 备份数据库
1.  登录 IP1 和 IP2，分别暂停两台机器上的 OMS 旧容器：oms_322bp2；
```shell
docker ps   # 查询所有正在运行的容器

# 停止 docker 容器运行
	# 语法：sudo docker stop ${CONTAINER_NAME}
	# 参数：CONTAINER_NAME：为创建容器的名称；

sudo docker stop oms_322bp2
sudo docker stop oms_322bp2
	#  登录 IP1 和 IP2，分别暂停两台机器上的 OMS 旧容器：oms_322bp2；
```


2.  登录配置文件中的 CM 心跳库，备份数据；

```shell
# 登录配置文件中的 CM 心跳库
# 语句示例：mysql -hxxx.xxx.xxx.xxx -P<port> -u<username> -p<password> -Dcm_hb_340

# 创建中间表
CREATE TABLE IF NOT EXISTS `heatbeat_sequence_bak` (
`id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'PK',
`gmt_created` datetime NOT NULL,
`gmt_modified` datetime NOT NULL,
PRIMARY KEY (`id`)
) DEFAULT CHARSET=utf8 COMMENT='心跳 sequence 表';

# 将需要的数据备份至中间表
INSERT INTO heatbeat_sequence_bak SELECT `id`,`gmt_created`,`gmt_modified` FROM heatbeat_sequence ORDER BY `id` DESC LIMIT 1;

# 重命名 heatbeat_sequence 表和中间表
# heatbeat_sequence 用于汇报心跳，获取自增 ID
ALTER TABLE `heatbeat_sequence` RENAME TO `heatbeat_sequence_bak2`;
ALTER TABLE `heatbeat_sequence_bak` RENAME TO `heatbeat_sequence`;

# 删除原来的表
DROP TABLE heatbeat_sequence_bak2;
```

3.  执行下述命令，手工备份 rm、cm 和 cm_hb 库为 SQL 文件，并确认各文件的大小不为 0；
多地域场景下，各地域的 cm_hb 库均需要备份。例如有两个地域，则需要备份 4 个库：rm、cm、cm_hb1 和 cm_hb2；

```sql
# 备份语句示例：
	# mysqldump -hxxx.xxx.xxx.xxx -P<port> -u<username> -p<password> --triggers=false rm_340 > /home/admin/rm_340.sql
	# mysqldump -hxxx.xxx.xxx.xxx -P<port> -u<username> -p<password> --triggers=false cm_340 > /home/admin/cm_340.sql
	# mysqldump -hxxx.xxx.xxx.xxx -P<port> -u<username> -p<password> --triggers=false cm_hb_340 > /home/admin/cm_hb_340.sql

# 实际备份语句：
```


##### 4.3 加载新容器并启动新容器
1. `加载下载的 OMS 安装包至 Docker 容器的本地镜像仓库`
```shell
# 1. 加载下载的 OMS 安装包至 Docker 容器的本地镜像仓库
	# 语法：docker load -i <OMS 安装包>

```

2. `启动 OMS V4.2.2 新容器`
 OMS 支持通过 HTTP 协议和 HTTPS 协议访问 OMS 控制台。如果您想要安全访问 OMS，可以配置 HTTPS 自签证书，并将其挂载至容器内的指定目录。如果使用 HTTP 协议访问 OMS，则无需配置；

> [!NOTE] `注意`：
> 1. 启动 OMS V4.2.2 新容器时，OMS 三个挂载盘的路径需要与升级前保持完全一致；
> 	1. 您可以执行 `sudo docker inspect ${CONTAINER_NAME} | grep -A5 'Binds'` 命令，查看之前旧 OMS 容器的挂载盘路径；
> 2. OMS V3.3.1 及之后版本新增参数 `-e IS_UPGRADE=true`。该参数仅适用于升级场景，且升级场景下必须添加该参数；

```shell
# 启动 OMS V4.2.2 新容器命令示例：
OMS_HOST_IP=xxx
CONTAINER_NAME=oms_xxx
IMAGE_TAG=feature_x.x.x

docker run -dit --net host \
-v /data/config.yaml:/home/admin/conf/config.yaml \
-v /data/oms/oms_logs:/home/admin/logs \
-v /data/oms/oms_store:/home/ds/store \
-v /data/oms/oms_run:/home/ds/run \
# 仅在 OMS 容器中挂载 HTTPS 证书时，需要设置如下两个参数
-v /data/oms/https_crt:/etc/pki/nginx/oms_server.crt
-v /data/oms/https_key:/etc/pki/nginx/oms_server.key
-e OMS_HOST_IP=${OMS_HOST_IP} \
-e IS_UPGRADE=true \
--privileged=true \
--pids-limit -1 \
--ulimit nproc=65535:65535 \
--name ${CONTAINER_NAME} \
work.oceanbase-dev.com/obartifact-store/oms:${IMAGE_TAG}
```

| 参数<img width=220/> | 描述 | 
| :-------------------------- | :---------- |
| OMS_HOST_IP |  宿主机的 IP 地址; 注意：每个节点的 `OMS_HOST_IP` 不一样; | 
| CONTAINER_NAME | 创建容器的名称。格式为 oms_xxx，请根据具体版本填写 xxx。例如，使用 OMS V4.2.2，则填写 oms_422； | 
| IMAGE_TAG | 使用 Docker 加载 OMS 安装包后，通过 `docker images` 命令获取到加载的相应镜像的 [IMAGE ID] 或 [REPOSITORY:TAG]，即为相应加载后镜像的唯一标识符 `<OMS_IMAGE>`； | 
| /data/oms/oms_logs <br> /data/oms/oms_store <br>  /data/oms/oms_run | `/data/oms/oms_logs`、`/data/oms/oms_store` 和 `/data/oms/oms_run` 可以替换为在您的 OMS 部署服务器上创建的挂载目录，分别存放 OMS 运行过程中产生的日志文件、日志拉取组件和同步组件生成的文件，在本机进行持久化；<br> **注意：**  在后续的版本重新部署、升级中，挂载目录的位置必须保持不变；| 
| /home/admin/logs <br> /home/ds/store  <br> /home/ds/run | `/home/admin/logs`、`/home/ds/store` 和 `/home/ds/run` 为容器内的固定目录，路径不可以修改； | 
| /data/oms/https_crt（可选）<br> /data/oms/https_key（可选） | HTTPS 证书在 OMS 容器内的挂载位置；<br> 如果挂载了 HTTPS 证书，OMS 容器内的 Nginx 服务将会运行在 HTTPS 模式下。您必须以 HTTPS 模式访问，才可以使用 OMS 的控制台服务；| 
| IS_UPGRADE | 升级场景下，必须设置 `IS_UPGRADE=true`。请注意 `IS_UPGRADE` 需要为大写； | 
| privileged | 为容器授予扩展权限 | 
| pids-limit | 配置容器的进程数限制，-1 表示不限制； | 
| ulimit nproc | 配置用户进程数的上限。 | 


```shell
# 1. 列出本地所有的镜像
docker images   # 获取加载后镜像的唯一标识符 <OMS_IMAGE>
	# 

# 2.启动 OMS V4.2.2 新容器命令:
## 100.63.40.216
OMS_HOST_IP=100.63.40.216
CONTAINER_NAME=oms_422bp1
IMAGE_TAG=feature_x.x.x

docker run -dit --net host \
-v /data/config.yaml:/home/admin/conf/config.yaml \
-v /data/oms/oms_logs:/home/admin/logs \
-v /data/oms/oms_store:/home/ds/store \
-v /data/oms/oms_run:/home/ds/run \
-e OMS_HOST_IP=${OMS_HOST_IP} \
-e IS_UPGRADE=true \
--privileged=true \
--pids-limit -1 \
--ulimit nproc=65535:65535 \
--name ${CONTAINER_NAME} \
work.oceanbase-dev.com/obartifact-store/oms:${IMAGE_TAG}

## 100.63.41.216 
```

##### 4.4 升级 JAR
1. 进入新容器
```shell
# 1.进入新容器
docker ps # 查询所有正在运行的容器

# 进入新容器
# 语法：docker exec -it ${CONTAINER_NAME} bash    
# 语法：docker exec -it 容器ID|容器名称 /bin/bash  
docker exec -it XXXX /bin/bash   ## 100.63.40.216  
docker exec -it XXXX /bin/bash   ## 100.63.41.216 
```

2. 暂停 oms_console
```shell
# 2.暂停 oms_console
supervisorctl status # 获取输出信息，确认各个服务均为 RUNNING 状态。 
	# RUNNING 状态说明各个服务状态正常，OMS 已在环境中正常运行

# 执行下述命令，暂停 oms_console，使其保持为 STOPPED 状态；
supervisorctl stop oms_console
```

3.  确认 CM/Supervisor 组件处于 RUNNING 状态后，请参考如下命令执行升级 JAR；
```shell
# 确认 CM/Supervisor 组件状态
supervisorctl status # 获取输出信息，确认各个服务均为 RUNNING 状态。 
	# RUNNING 状态说明各个服务状态正常，OMS 已在环境中正常运行

# 升级 JAR:
## 命令示例：
	/opt/alibaba/java/bin/java -jar correction-1.0-SNAPSHOT-jar-with-dependencies.jar -mupgrade -y/home/admin/conf/config.yaml -ltrue
# 参数介绍：
	-m：运行模式，支持 UPGRADE（执行升级操作）；
	-y：OMS 配置文件的绝对路径；
	-l：表示是否是最后一个升级节点。
		单地域场景下，本参数需要设置为 `true`。
		多地域场景下，前面几个地域升级时，需要设置为 `false`，且执行直到最后一个地域升级时，才可以设置为 `true`；  
		说明：多地域多节点场景下，每个地域只需要选取第一个节点执行升级 JAR 即可。最后一个地域执行升级时，需要将 `-l` 参数设置为 `true`；

## 实际升级命令：
	# 注意：实际执行时，请根据实际情况替换参数值；
# 100.63.40.216
/opt/alibaba/java/bin/java -jar correction-1.0-SNAPSHOT-jar-with-dependencies.jar -mupgrade -y/home/admin/conf/config.yaml -ltrue

# 100.63.41.216
/opt/alibaba/java/bin/java -jar correction-1.0-SNAPSHOT-jar-with-dependencies.jar -mupgrade -y/home/admin/conf/config.yaml -ltrue
```

##### 4.5 初始化元数据初始化
执行完成升级 JAR 后，在 `root` 目录下，执行元数据初始化操作；

> [!NOTE] `注意事项`：
> 1. `必须保证 OMS 每个节点都完成升级 JAR`；
> 	1. 如果没有全部节点均执行完成升级 JAR 便执行了元数据初始化操作，会导致部分地域的元数据未更新，直接启动服务会存在问题；
> 2. 执行以下命令后，脚本会自动将 OMS 三个数据库的 Schema 变更执行到数据库中；

1. 如果每个地域内的 `cm_nodes` 是一致的，多节点升级时，您只需要在其中一个节点执行 `docker_init.sh` 命令，其它节点请分别进行以下操作：
	1. 执行 `sed -i 's/autostart = false/autostart = true/g' /etc/supervisor/conf.d/oms_console.ini` 命令；
	2.  执行 `supervisorctl status;`，检查组件的状态；
	3.  如果 `oms_console` 未运行，请执行 `supervisorctl start oms_console` 命令；
2. 如果每个地域内的 `cm_nodes` 是不一致的，您需要在每个节点都执行一次 `docker_init.sh` 命令；

```shell
# 升级实际执行命令：
	# 必须保证 OMS 每个节点都完成升级 JAR；
## 100.63.40.216
cd root
docker_init.sh

## 100.63.41.216
cd root
sed -i 's/autostart = false/autostart = true/g' /etc/supervisor/conf.d/oms_console.ini
supervisorctl status # 获取输出信息，确认各个服务均为 RUNNING 状态。 
	# RUNNING 状态说明各个服务状态正常，OMS 已在环境中正常运行
	# 若 oms_console 组件未运行，请执行 supervisorctl start oms_console 命令；
```

##### 4.6 验证 OMS 状态并恢复 OMS 服务
1. `docker_init.sh` 脚本执行完成后，请确认机器列表是否正常，且机器均处于 **在线** 状态；
2. 依次升级完两个节点后，在 **系统参数** 页面，开启 HA 功能，并设置相关参数；
	1. 开启 HA 功能：将 ha.config 参数的值设置 false 为 enable，即开启 HA 功能；
	2. 建议您设置 `perceiveStoreClientCheckpoint` 为 `true`，设置后则无需记录 T1 和 T2 时间；

#### 5 OMS 升级回滚操作
若 OMS 升级出现问题或升级后出现问题可以根据以下步骤进行回滚操作；

1.  根据第 1 步的操作，关闭 HA 功能；
在 OMS 系统中修改 ha.config 参数的值，将其值设置 enable 为 false，即关闭 HA 功能；

2.  暂停升级后的新容器，记录时间 T3；
```shell
docker ps   # 查询所有正在运行的容器

# 停止 docker 容器运行
	# 语法：sudo docker stop ${CONTAINER_NAME}
	# 参数：CONTAINER_NAME：为创建容器的名称；

sudo docker stop oms_422bp1
sudo docker stop oms_422bp1
	#  登录 IP1 和 IP2，分别暂停两台机器上的 OMS 新容器：oms_422bp1；
```
    
3.  连接元数据库，执行下述命令。
```sql
-- 连接数据库

-- 删除并重建数据库
drop database rm_340;
drop database cm_340;
drop database cm_hb_340;

create database rm_340;
create database cm_340;
create database cm_hb_340;
```
    
4.  将第 2 步中备份的数据库 SQL 文件，还原为原数据库。
```shell
# 恢复数据库示例：
	# mysql -hxxx.xxx.xxx.xxx -P<port> -u<username> -p<password> -e "source /home/admin/rm_340.sql" -Drm_340
	# mysql -hxxx.xxx.xxx.xxx -P<port> -u<username> -p<password> -e "source /home/admin/cm_340.sql" -Dcm_340
	# mysql -hxxx.xxx.xxx.xxx -P<port> -u<username> -p<password> -e "source /home/admin/cm_hb_340.sql" -Dcm_hb_340

# 恢复数据库实际语句：
	
```
    
5.  重启 OMS V3.4.0 旧容器。
```shell
docker stats  # 查看所有容器的状态

# 重启容器
# 语法：sudo docker restart ${CONTAINER_NAME}
sudo docker restart oms_322bp2      # 100.63.40.216
sudo docker restart oms_322bp2      # 100.63.41.216
	# 重启所有节点的 OMS 旧容器；
```
    
6.  在 **系统参数** 页面，开启 HA 功能；
将 ha.config 参数的值设置 false 为 enable，即开启 HA 功能；

> [!NOTE] `说明`：
> 1. 建议您设置 `perceiveStoreClientCheckpoint` 为 `true`；
> 2. HA 功能开启后会自动容灾 Store，并自动拉起 Incr-Sync 组件，您还需要手动恢复 Full-Import 组件；

### 参考文档
1. [OMS 多节点升级_V4.2.2](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000613356)，；


