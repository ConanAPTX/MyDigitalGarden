---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/OMS 迁移数据/OMS 运维监控/管理 OMS 服务/","dgPassFrontmatter":true}
---



### 管理 OMS 服务
可以使用 `supervisorctl` 命令管理 OMS 服务，包括启动、停止、重启和查看服务；

```shell
# 语法：
	supervisorctl <start | stop | restart | status > <oms_console | oms_drc_cm | oms_drc_supervisor >

# 参数：
	start：	启动服务。
	stop：停止服务。
	restart：重启服务。
	status：查看服务状态。
	oms_console：管理 Console 服务。
	oms_drc_cm：管理 CM 服务。
	oms_drc_supervisor：管理 Supervisor 服务。
```


#### 1 前提条件
在管理 OMS 服务之前，请确保您已经登录到部署 OMS 的机器，并且您已经进入到 OMS 的容器。执行以下命令，进入部署 OMS 的容器：

```bash
# 1.进入 Docker 容器
docker ps   # 查询所有正在运行的容器

docker exec -it ${CONTAINER_NAME} bash       # CONTAINER_NAME：为创建容器的名称；
docker exec -it <oms_container_name> bash
```

进入 OMS 的容器后，即可执行 `supervisorctl` 命令。


#### 2 查看 OMS 服务

```bash
# 查看 OMS 服务
# 检查 OMS 各个服务状态，执行 supervisorctl status 命令，获取输出信息，确认各个服务均为 RUNNING 状态；
supervisorctl status
supervisorctl status oms_console    # 查看 OMS 服务，以及 Console 服务为例；
	# RUNNING 状态说明各个服务状态正常，OMS 已在环境中正常运行；
	# OMS 的组件包括管控组件和数据迁移链路组件，管控组件包括 console、cm 和 supervisor；
# 输出：
	nginx                            RUNNING   pid 905, uptime 3 days, 5:40:09
	oms_console                      RUNNING   pid 907, uptime 3 days, 5:39:59
	oms_drc_cm                       RUNNING   pid 971, uptime 3 days, 5:39:49
	oms_drc_supervisor               RUNNING   pid 1223, uptime 3 days, 5:39:38
	sshd                             RUNNING   pid 1419, uptime 3 days, 5:39:27
```


#### 3 启动，停止，重新启动服务

```bash
# 启动 OMS 服务
supervisorctl start oms_console          # 启动 OMS 服务，以及 Console 服务为例；
	## 输出：oms_console: started；	
	## 若 Console 组件启动成功，返回以上结果；

# 停止 OMS 服务
supervisorctl stop oms_console           # 停止 OMS 服务，以及 Console 服务为例；
	## 输出：oms_console: stopped
	## 若 Console 组件停止成功，返回以上结果；

# 重新启动 OMS 服务
supervisorctl restart oms_console        # 重新启动 OMS 服务，以及 Console 服务为例；
	## 输出：
		oms_console: stopped
		oms_console: started
	## 若 Console 组件重新启动成功，返回以上结果；
```



### 参考文档
1. *管理 OMS 服务*：[V4.2.3](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000987924)，；


