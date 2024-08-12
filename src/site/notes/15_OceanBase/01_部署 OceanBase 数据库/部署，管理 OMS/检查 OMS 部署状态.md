---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OMS/检查 OMS 部署状态/","dgPassFrontmatter":true}
---


### 检查 OMS 部署状态
在部署服务器上安装 OMS 之后，还需要进入 Docker 查看各个服务的运行状态，判断 OMS 是否已在部署服务器上正常运行；

#### 1 检查  OMS 部署状态
```shell
# 1.进入 Docker 容器
docker ps   # 查询所有正在运行的容器

docker exec -it ${CONTAINER_NAME} bash
	# CONTAINER_NAME：为创建容器的名称；

# 2.检查 OMS 各个服务状态，执行 supervisorctl status 命令，获取输出信息，确认各个服务均为 RUNNING 状态；
supervisorctl status
	# RUNNING 状态说明各个服务状态正常，OMS 已在环境中正常运行；
	# OMS 的组件包括管控组件和数据迁移链路组件，管控组件包括 console、cm 和 supervisor；
# 输出：
	nginx                            RUNNING   pid 905, uptime 3 days, 5:40:09
	oms_console                      RUNNING   pid 907, uptime 3 days, 5:39:59
	oms_drc_cm                       RUNNING   pid 971, uptime 3 days, 5:39:49
	oms_drc_supervisor               RUNNING   pid 1223, uptime 3 days, 5:39:38
	sshd                             RUNNING   pid 1419, uptime 3 days, 5:39:27

# 3.登录 OMS 控制台，在 **机器列表** 页面确认机器数量是否符合预期。
```
详情请参见 OMS 用户指南中的 [登录 OMS 控制台](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000613321) 和 [查看机器信息](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000613422)，；

#### 2 组件启动失败处理
如果管控组件启动失败，可能是因为内存问题，您可以进行下述操作调整组件的启动配置：

1. 根据上述操作获取的输出信息，确认管控组件是否启动；
2. 进入 `/etc/supervisor/conf.d/` 目录，执行 `ll` 命令，查看各管控组件的初始化文件名称；
```shell
# cd /etc/supervisor/conf.d/
# ll
total 20
-rw-r--r-- 1 root root 285 Jan 13 15:37 base.ini
-rw-r--r-- 1 root root 338 Jan 13 15:37 drc_cm.ini
-rw-r--r-- 1 root root 397 Jan 13 15:37 drc_supervisor.ini
-rw-r--r-- 1 root root 351 Jan 13 15:37 oms_console.ini
-rw-r--r-- 1 root root 316 Jan 13 15:37 oms_nginx.ini
```

3.  根据业务需求，执行 `cat` 命令，查看相应组件的启动脚本。其中 command 命令中，bash 后的内容为启动脚本。
	-   执行 `cat oms_console.ini` 命令，查看 console 对应的启动脚本为 `/home/admin/conf/command/start_oms_console.sh`。
	-   执行 `cat drc_cm.ini` 命令，查看 cm 对应的启动脚本为 `/home/admin/conf/command/start_oms_cm.sh`。
	-   执行 `cat drc_supervisor.ini` 命令，查看 supervisor 对应的启动脚本为 `/home/ds/supervisor/service.sh`。
 
4.  找到对应管控组件的启动脚本后，自行更改 JVM 启动内存的配置。
5.  执行 `supervisorctl restart` 命令，重新启动对应管控组件。
如果在执行 `docker_init.sh` 过程中失败，您可以修改配置后，直接重新启动 `docker_init.sh`。


### 参考文档
1. [检查部署状态](https://www.oceanbase.com/docs/enterprise-oms-doc-cn-1000000000613360)，检查 OMS 部署状态，及组件启动失败处理；

