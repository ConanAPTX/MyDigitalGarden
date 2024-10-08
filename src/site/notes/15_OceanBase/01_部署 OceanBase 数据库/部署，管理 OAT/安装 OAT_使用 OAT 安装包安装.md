---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OAT/安装 OAT_使用 OAT 安装包安装/","dgPassFrontmatter":true}
---


### 安装 OAT_使用 OAT 安装包安装
#### 1 前提条件

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/01-ocean-base/oat/oat-oat-all-in-one/#1" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



#### 1 前提条件
在部署 OAT 之前，您需要确认以下信息：

##### 1.1 您的操作系统满足以下条件：
| 服务器类型   | 操作系统 |   支持版本 |
| :------------- | :---------- | :------------ |
| x86_64 |   RHEL   | 7.2  |    及以上版本 |
| x86_64  |   	CentOS 	7.2  |    及以上版本  |   
| x86_64  |   	AliOS 	7.2  |    及以上版本  |   
| x86_64  |   	openSUSE   |   	12SP3 及以上 |   
| ARM aarch64  |   	AliOS   |   	7.2 及以上版本 |   
| ARM aarch64  |   	中标麒麟  |   	7.6 |   
| ARM aarch64  |   	华为 EulerOS  |   	2.0 SP8 |   

##### 1.2 您已经安装并启动 Docker。推荐 Docker 版本为社区版 17.03 及以上；

##### 1.3 OAT 的默认端口 `7000` 未被占用；
```shell
netstat -anp | grep 7000      # 查询端口是否被占用
```



</div></div>


#### 2 操作步骤 
##### 2.1 下载 OAT 安装包，并拷贝
需要联系 OceanBase 的技术人员下载安装包；

```shell
# 使用 scp 命令将 OAT 安装包上传到服务器中
# 语法：scp <oat_directory> <oat_server_user>@:<oat_server_ip>   # 其中 oat_server 为部署 OAT 的服务器的 IP 地址

```


##### 2.2 安装并运行

```shell
mkdir -p /data_dir    # 在服务器创建 /data_dir 目录用来保存 OAT 的持久化数据
	# 在 /data_dir 目录被挂载到 OAT 容器后，OAT 会自动创建 /data_dir/logs、/data_dir/images 和 /data_dir/db 目录，
	# 分别用于存放 OAT 的系统日志、组件和产品的 Docker 镜像以及 OAT 的数据库文件

docker load -i oat.tar   # 将 OAT 安装包装载为镜像

# 用 docker images 命令获取 OAT 镜像的标签
oat_image=`docker images | grep oat | awk '{printf $1":"$2"\n"}'`
	# 说明：当只加载了一个 OAT 安装包时，使用示例命令即可。当加载了多个安装包时，使用 docker images 命令查看显示内容，并使用冒号（:）连接前两列的内容；


# 运行 docker run 命令，启动 OAT
docker run -d --name oat -v /data_dir:/data -p 7000:7000 --restart on-failure:5 $oat_image
```

##### 2.3 登录验证

```shell 
# 在浏览器地址栏输入 http://<<oat_server_ip>:7000
	# OAT 默认用户名为 admin，默认密码为 aaAA11__。首次登录后您必须修改密码；
	# 若在浏览器窗口中看到登录界面，表示已经成功安装并启动 OAT；
```



### 参考文档

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/01-ocean-base/oat/oat-oat-all-in-one/#" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



### 参考文档
1. `安装 OAT`：[V4.0.0](https://www.oceanbase.com/docs/enterprise-oat-doc-cn-10000000001092535)，[V4.2.0](https://www.oceanbase.com/docs/enterprise-oat-doc-cn-1000000000304881)，[V4.2.1](https://www.oceanbase.com/docs/enterprise-oat-doc-cn-1000000000762607)，；




</div></div>




