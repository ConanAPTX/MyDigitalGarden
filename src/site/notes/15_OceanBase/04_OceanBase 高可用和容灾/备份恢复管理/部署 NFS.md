---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/04_OceanBase 高可用和容灾/备份恢复管理/部署 NFS/","dgPassFrontmatter":true}
---


### 部署 NFS

> `注意事项：`
> 1. 使用 NFS 环境时，需要保证先挂载 NFS，再开启备份。如果备份期间 NFS 出现问题，需要先停止数据备份和日志备份，再解决 NFS 的问题；
> 2. 由于 OceanBase 数据库备份的并发控制需依赖 NFS4 的文件锁功能，故在挂载 NFS 时，需使用 NFS 4.1 及以上版本；
> 3. 在使用 NFS 作为备份介质时，必须保证所有 OBServer 都挂载了同一个服务器的 NFS。同时，为保证备份的顺利进行，务必使用本文档中推荐的参数挂载 NFS；
> 4. 在重启 OBServer 所在的服务器时，需要先启动 NFS 服务，再启动 OBServer 服务；
> 5. 添加新的机器后，在启动 OBServer 前，需要保证新的机器挂载 NFS 成功或者可以备份到其他介质；



#### 1. 部署 NFS 软件的服务器端
> 注意事项：
> 	1. 若使用的是 NFS 硬件设备，则可跳过本操作，直接部署 NFS 客户端；

##### 1. 安装 NFS
```shell
# 登录 NFS 服务器。
# 执行以下命令，通过 YUM 包管理器安装 NFS；  
sudo yum install nfs-utils    
```

##### 2. 设置 Exports
```shell
# 1. 选择一个合适的目录作为共享目录，在选择时需要考虑备份所需的空间和性能
	# 例如，本文档中选择的共享目录为 `/data/nfs_server/`

# 2. 使用 `sudo vim /etc/exports` 命令打开配置文件，设置以下信息
	/data/nfs_server/ 100.xx.xx.xx/16(rw,sync,all_squash)
		# `100.xx.xx.xx` 表示允许访问的网段

# 3. 执行以下命令，为 `nfsnobody` 赋权，确保 `nfsnobody` 有权限访问 `exports` 中指定的目录
	sudo chown nfsnobody:nfsnobody -R /data/nfs_server
```

##### 3. 配置 NFS 参数
```shell
sudo vim /etc/sysconfig/nfs
	# 调整如下所示参数：
	RPCNFSDCOUNT=8
	RPCNFSDARGS="-N 2 -N 3 -U"
	NFSD_V4_GRACE=90
	NFSD_V4_LEASE=90

# 重新启动 NFS
sudo systemctl restart nfs-config
sudo systemctl restart nfs-server
```

##### 4. 设置 Slot Table
```shell
sudo vim /etc/sysctl.conf
	# 在文件中添加一行如下信息
	sunrpc.tcp_max_slot_table_entries=128

# 将同时发起的 NFS 请求数量修改为 128
sudo sysctl -w sunrpc.tcp_max_slot_table_entries=128

# 验证设置是否生效
cat /proc/sys/sunrpc/tcp_max_slot_table_entries
	# 如果返回值为 128，则说明修改成功；
```


#### 2. 部署 NFS 客户端
> `注意`：1. 部署 NFS 客户端时，需要在所有 OBServer 机器上进行操作；

##### 1. 安装并配置 NFS 客户端
```shell  
# 1. 执行以下命令，通过 YUM 包管理器安装 NFS
	# 登录 OBServer
    sudo yum install nfs-utils
    
    
# 2. 设置 Slot Table。
	sudo vim /etc/sysctl.conf   # 打开 sysctl.con 配置文件，在文件中添加一行如下信息
		sunrpc.tcp_max_slot_table_entries=128

	sudo sysctl -w sunrpc.tcp_max_slot_table_entries=128 # 执行以下命令，将同时发起的 NFS 请求数量修改为 128

	# 查看设置是否生效
	cat /proc/sys/sunrpc/tcp_max_slot_table_entries  
		# 如果返回值为 128，则说明修改成功
        
# 3. 重启机器(可选)

```

##### 2. 挂载 NFS 并验证
```shell
# 1. 选择一个合适的目录作为 Mount 点， 执行以下命令，挂载 NFS
# 本文档中假设 Mount 到 `/data/nfs` 目录，如果没有合适的目录可以新建一个目录
sudo mount -tnfs4 -o rw,nfsvers=4.1,sync,lookupcache=positive,hard,timeo=600,wsize=1048576,rsize=1048576,namlen=255 100.xx.xx.xx:/data/nfs_server /data/nfs
<<<#####
`nfsvers=4.1`：由于备份依赖 nfs 4 原生的文件锁，推荐使用 nfs 4.1 及以上版本。nfs 4.0 有一个已知 Bug，在重命名文件以后可能会读到旧文件。  
`sync`：使用同步写保证数据能及时刷到服务端，从而保证数据的一致性。
`lookupcache=positive`：用于避免并发访问目录或者文件时误报目录或文件不存在的问题，保证数据的一致性。
`hard`：在 NFS 不可用的情况下，系统会卡住应用的读写请求，以保证数据的一致性。不能使用 `soft` 选项，会有数据错误的风险。
`timeo`：用于指定重试的等待时间，单位为 0.1s。在设置时，建议不要设置得过大，推荐值为 `600`。
`wsize`：表示读的数据块大小，建议设置为 `1048576`。
`rsize`：表示写的数据库大小，建议设置为 `1048576`。
`namlen`：建议设置为 `255`。
`100.xx.xx.xx`：表示 NFS 服务器的 IP 地址
#####

# 2. 挂载完成后，可执行以下命令，验证 NFS 的性能
fio -filename=/data/nfs/fio_test -direct=1  -rw=randwrite -bs=2048K -size=100G  -runtime=300 -group_reporting -name=mytest  -ioengine=libaio -numjobs=1 -iodepth=64 -iodepth_batch=8 -iodepth_low=8 -iodepth_batch_complete=8
# 输出结果:
# Run status group 0 (all jobs):
# WRITE: io=322240MB, aggrb=1074.2MB/s, minb=1074.2MB/s, maxb=1074.2MB/s, mint=300006msec, maxt=300006msec


```



### 参考文档：
1. [部署 NFS](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000000944110)，；