---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/20_OceanBase 常用 Sql 语句/获取，拷贝，分析 OB 相关日志/拷贝 Oceanbase 日志文件_2/","dgPassFrontmatter":true}
---


### 拷贝 Oceanbase 日志文件_2
#### 1 创建文件夹
创建文件夹用来保存日志文件；
```shell
# 跳板机(100.63.41.87)上执行；
sudo mkdir /tmp/conan/  
sudo mkdir -p /tmp/conan/20240614/ # 循环创建文件夹  
  
cd /tmp/conan/20240614/  
sudo mkdir ./{218,214,213}/  
```

  
#### 2 拷贝文件到跳板机  
【zone2(100.63.41.218); zone1(100.63.41.214); zone3(100.63.40.213);】  

```shell
# OB 集群一般有多台 OBserver 机器
ssh 100.63.41.218   ssh 100.63.41.214   ssh 100.63.41.213

## 查看日志文件
	hostname -i  
	cd /home/admin/oceanbase/log  
	cd /home/admin/logs/obproxy/log 
	cd /home/admin/obproxy/log         # 在 4.x 版本中在该目录
		# obproxy.log obproxy_error.log obproxy_digest.log 

## 在 OBServer 机器上拷贝文件并进行压缩；【若每台机器需要取多个日志】
	sudo mkdir /tmp/conan/ 
	sudo mkdir -p /tmp/conan/20231219/ # 循环创建文件夹
	sudo cp /home/admin/oceanbase/log/observer.log.20240614154001 /tmp/conan/20231219/
	sudo cp /home/admin/oceanbase/log/{文件名1,文件名2} /tmp/conan/20231219/             # 语法未验证

	### 压缩日志
	cd /tmp/conan/ 
	tar -cvf ./20231219_100.64.180.20.tar ./20231219/        # 打包(文件大小不变) 
	tar -zcvf ./20231219_100.64.180.20.tar.gz ./20231219/    # 把文件或文件夹打包后，压缩成一个小文件夹

	### 若需要删除文件
		rm file.txt ## 删除当前目录下的指定文件

## 拷贝文件，跳板机上执行；
	scp 100.63.41.218:/tmp/conan/20231219_100.64.180.20.tar.gz /tmp/conan/20240614/218 
	scp 100.63.41.218:/home/admin/oceanbase/log/observer.log.20240614154001 /tmp/conan/20240614/218  
	scp 100.63.41.218:/home/admin/logs/obproxy/log/obproxy.104975.log.20240614165624 /tmp/conan/20240614/218
```

  
#### 3 压缩文档并下载
```shell
# 在跳板机上执行：一般在 87 上执行；
## 压缩文档
	cd /tmp/conan/  
	tar -zcvf ./20240614.tar.gz ./20240614/  

## 将文件从跳板机拷贝到本地（通过 SecureCRT 连上跳板服务器）
	cd /tmp/conan/  
	ls | grep observer.log.wf.20230703185604  
	sz ./observer.log.wf.20230703185604  
	sz ./20231219_100.63.41.218.tar.gz  
		# 下载到本地的文件默认保存目录：C:\Users\szy\Downloads
```




### 参考文档



