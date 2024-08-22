---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/导数工具/安装 mydumper，myloader 工具/","dgPassFrontmatter":true}
---


### 安装 mydumper，myloader 工具
#### 1 mydumper，myloader 工具概述
[[11_MySQL/04_安全与保护/MySql 备份恢复/mydumper，myloader 概述\|mydumper，myloader 概述]]，；

#### 2 安装 mydumper，myloader 工具
##### 2.1 直接安装 rpm 包
请根据需要在 [下载安装包地址](https://github.com/mydumper/mydumper/tags) 中，下载对应的安装包并安装；

```shell
# 下载安装包
cd /opt
wget https://github.com/mydumper/mydumper/releases/download/v0.15.2-6/mydumper-0.15.2-6.el7.x86_64.rpm

# 在数据库主机上解压缩安装包
rpm2cpio mydumper-0.12.7-2-zstd.el7.x86_64.rpm | cpio -div
rpm2cpio mydumper-0.15.2-6.el7.x86_64.rpm | cpio -div

# 验证是否安装完成
ls /usr/bin | grep my   # 安装完成后生成两个二进制文件 mydumper，myloader;
/usr/bin/mydumper --help
/usr/bin/mydumper -V
	# mydumper 0.12.7-2, built against MySQL 5.7.39-42
```


##### 2.2 编译安装


### 参考文档
1. [mydumper，myloader 工具下载地址](https://github.com/mydumper/mydumper/tags)，；



