---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/01_部署 OceanBase 数据库/部署，管理 OceanBase 数据库/拷贝 Oceanbase 日志文件/","dgPassFrontmatter":true}
---


### 拷贝 Oceanbase 日志文件

 将文件拷贝到本地，或拷贝到服务器；
 
#### 1 将文件从【服务器】拷贝到【本地】 
一般主要是将 OceanBase 数据库各种日志拷贝到本地；

###### 1.1.1 observer 服务器上执行

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/15-ocean-base/01-ocean-base/ocean-base/observer/#1" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



##### 1 通过跳板机登录指定服务器及目录
```shell
# 1.通过跳板机登录指定服务器  
	ssh 100.64.132.29 ssh 100.64.132.30 ssh 100.64.132.94  
	ip addr    # 查询服务器 ip 地址

# 2.进入日志目录
	cd /home/admin/oceanbase/log        # OB Server 日志目录  
		# 若未使用默认目录，可以使用下面的命令查询实际目录  
		find /home -name "clog"
	cd /home/admin/logs/obproxy/log     # OB proxy 日志目录  
	ll |grep 2023070318  
```



</div></div>

```shell
# 3.创建文件夹  
sudo mkdir /tmp/conan/  
sudo mkdir -p /tmp/conan/20231031/ # 循环创建文件夹  
# sudo mv ./2883_2881_100.* /tmp/conan/20231031_100.64.180.20/ # 移动文件夹  
  
# 4.压缩文件  
cd /tmp/conan/  
tar -cvf ./20231031_100.64.180.20.tar ./20231031/      # 打包(文件大小不变)  
tar -zcvf ./20231031_100.64.180.20.tar.gz ./20231031/  # 把文件或文件夹打包后，压缩成一个小文件夹 
```
  
###### 1.1.2 在跳板机上执行
```shell
# 在跳板机上执行  
## 1.创建文件夹  
sudo mkdir /tmp/conan/  
sudo mkdir -p /tmp/conan/20231219/ # 循环创建文件夹  
  
## 2.将文件拷贝到跳板机上，语法：scp [文件所在服务器]:[文件所在服务器全路径名称] [跳板机路径]  
cd /tmp/conan/20231219/  
scp 100.63.41.218:/home/admin/oceanbase/log/observer.log.20231219091227 /tmp/conan/20231219/  
scp 100.63.41.218:/home/admin/oceanbase/log/observer.log.20231219091626 /tmp/conan/20231219/  
	# 在跳板机上执行；  
  
## 3.压缩文件(若文件在原服务器未压缩，可在跳板机上进行压缩)  
cd /tmp/conan/  
tar -cvf ./20231031_100.64.180.20.tar ./20231031/      # 打包(文件大小不变)  
tar -zcvf ./20231219_100.63.41.218.tar.gz ./20231219/  # 把文件或文件夹打包后，压缩成一个小文件夹  

## 4.将文件从跳板机拷贝到本地（通过 SecureCRT 连上跳板服务器）
cd /tmp/conan/  
ls | grep observer.log.wf.20230703185604  
sz ./observer.log.wf.20230703185604  
sz ./20231219_100.63.41.218.tar.gz  
	# 下载到本地的文件默认保存目录：C:\Users\szy\Downloads  
```

 
#### 2 将文件从【本地】拷贝到【服务器】
```shell
# 通过 SecureCRT 连上跳板服务器  
cd /opt/sun_20230901  
rz    # 选择需要上传的文件
```


### 参考文档



