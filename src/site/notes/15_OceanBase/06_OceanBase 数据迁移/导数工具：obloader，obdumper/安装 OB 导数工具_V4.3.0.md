---
{"number headings":"auto, first-level 4, max 6, contents
{ #toc}
, start-at 1, _.1.1","dg-publish":true,"permalink":"/15_OceanBase/06_OceanBase 数据迁移/导数工具：obloader，obdumper/安装 OB 导数工具_V4.3.0/","dgPassFrontmatter":true}
---


### 安装 OB 导数工具_V4.3.0
#### 1 适用版本
V4.3.0 版本； 


#### 2 准备环境
##### 2.1 运行环境
| 环境 | 要求 | 备注 |
| :------------- | :---------- | :------------ |
| 系统版本 | 支持 Linux/macOS/Windows 7 及之后版本。 |  |
| Java 环境 | 请安装 Oracle JDK 1.8.0_3xx，配置 JAVA_HOME 环境变量 |  |
| 字符集 |  推荐使用 UTF-8 文件编码 |  |
| JVM 参数 | 请编辑 bin/obloader 和 bin/obdumper 脚本修改 JVM 内存参数，避免出现 JVM 内存不足 |  |
  
> [!NOTE] *说明*：
> OpenJDK 1.8 的部分小版本存在严重的垃圾收集器（GC）错误，运行导入导出工具时可能会出现内存溢出或程序卡死。为了避免这些问题，我们建议您安装 [OpenJDK 1.8](https://pkgs.org/download/java-1.8.0-openjdk) 的最新小版本。
> 如果您正在使用的是麒麟等国产操作系统，您需要将 JVM 参数（即 JAVA_OPTS）中的 GC 回收器从 G1 调整为 CMS，否则可能会出现 JVM 崩溃的问题。
> 如果您正在使用 obloader/obdumper V4.3.0 之前的版本，请编辑 `bin/obloader` 文件，将 GC 参数从 `-XX:+UseG1GC` 修改为 `-XX:+UseConcMarkSweepGC`。
> 如果您正在使用 obloader/obdumper V4.3.0 及之后的版本，请编辑 `bin/obloader` 文件，修改 `GC_OPTS` 参数；
   

##### 2.2 运行权限
###### 2.2.1 OBLOADER 运行权限
使用 *OBLOADER* 连接 OceanBase 数据库导入数据时，连接数据库的账号需要拥有 `CREATE`, `SELECT`, `INSERT`, `UPDATE` 等命令的执行权限。可通过 `SHOW GRANTS` 语句查看用户被授予的权限。示例语句：

```sql
obclient> SHOW GRANTS FOR user1;
+------------------------------------------+
| Grants for user1@%                       |
+------------------------------------------+
| GRANT CREATE ON *.* TO 'user1'           |
| GRANT SELECT ON `db1`.* TO 'user1'       |
| GRANT INSERT ON `db1`.* TO 'user1'       |
| GRANT UPDATE ON `db1`.* TO 'user1'       |
| GRANT SELECT ON `oceanbase`.* TO 'user1' |
+------------------------------------------+
2 rows in set

-- 如果是 OceanBase 数据库 MySQL 租户，导入数据前，用户需要具备 `oceanbase` 数据库的查询权限。授权语句示例：
GRANT SELECT ON `oceanbase`.* TO 'user1';

-- 如果是 OceanBase 数据库 Oracle 租户，导入数据前，用户需要具备 `SYS` 的查询权限。授权语句示例：
GRANT SELECT ON "SYS".* TO 'user1';
```

> [!NOTE] `说明`：
> 1. 使用 OBLOADER 连接 OceanBase 数据库 Oracle 租户导入数据库对象定义和数据时，建议使用数据库管理员（DBA）角色的账号连接数据库。


###### 2.2.2 OBDUMPER 运行权限
使用 *OBDUMPER* 连接 OceanBase 数据库导出数据时，连接数据库的账号需要拥有 `CREATE`, `SELECT` 等命令的执行权限。可通过 `SHOW GRANTS` 语句查看用户被授予的权限。示例语句：

```shell
obclient> SHOW GRANTS FOR user1;
+------------------------------------------+
| Grants for user1@%                       |
+------------------------------------------+
| GRANT CREATE ON *.* TO 'user1'           |
| GRANT SELECT ON `db1`.* TO 'user1'       |
| GRANT SELECT ON `oceanbase`.* TO 'user1' |
+------------------------------------------+
2 rows in set
```



> [!NOTE] `说明`：
> 1. OBDUMPER 只有导出非表的数据库对象时，需要系统表的权限。
> 	1. 如果是 OceanBase 数据库 MySQL 租户，导出数据前，用户需要具备 `oceanbase` 数据库的查询权限。授权语句示例：``GRANT SELECT ON `oceanbase`.* TO 'user1'``。
> 	2. 如果是 OceanBase 数据库 Oracle 租户，导出数据前，用户需要具备 `SYS` 的查询权限。授权语句示例：`GRANT SELECT ON "SYS".* TO 'user1'`。
> 2. 使用 OBDUMPER 连接 OceanBase 数据库 Oracle 租户导出数据库对象定义时，建议使用数据库管理员（DBA）角色的账号连接数据库；使用 OBDUMPER 连接 OceanBase 数据库 Oracle 租户导出数据时，连接数据库的账号无角色要求。


##### 2.3 其它说明
-   OceanBase 数据库 V4.0.0 之前的版本，导数工具命令行中需要指定 `--sys-user`, `--sys-password` 选项；
-   命令行选项 `--sys-user`, `--sys-password` 必须使用 sys 租户下拥有查询系统表和查询视图权限的用户；


#### 3 下载并安装
##### 3.1 下载安装包
导数工具是一个软件压缩包，用户只需要在宿主机上解压即可运行该工具；*导数工具 V4.2.1 及之后的版本不再区分企业版和社区版*，请在 [OceanBase 软件中心](https://www.oceanbase.com/softwarecenter) 下载导数工具软件包；


##### 3.2 解压并使用导数工具
`Windows`：
```shell
# Windows
## 1.解压安装包	
	# 直接用解压软件进行解压即可；

## 2.进入运行脚本所在的目录
cd {ob-loader-dumper}/bin/windows 

## 3.查看命令行选项的帮助
call obloader.bat --help    
call obdumper.bat --help  

```

`Linux or macOS`：
```shell
# Linux or macOS
## 1.解压安装包
	### Linux 解压
	unzip ob-loader-dumper-4.2.x-RELEASE.zip
	unzip ob-loader-dumper-4.3.0-RELEASE.zip

## 2.进入运行脚本所在的目录
	# cd {ob-loader-dumper}/bin 
	cd ob-loader-dumper-4.3.0-RELEASE/bin

## 3.查看命令行选项的帮助
./obloader --help
./obdumper --help
```

关于 Linux 系统文件压缩：[[06_Linux/12_Linux 文件，目录与磁盘格式/8. 文件与文件系统的压缩/Linux 文件压缩以及解压\|Linux 文件压缩以及解压]]，；


#### 4 步骤 2：配置运行环境

> [!NOTE] `注意`：
> 用户的本地环境中必须安装 Java 8+ 并配置 `JAVA_HOME` 环境变量。强烈建议安装 JDK 1.8.0_3xx 及之后的版本。环境配置详情请参考 [准备环境](https://www.oceanbase.com/docs/common-oceanbase-dumper-loader-1000000000775411)，；

本步骤旨在修改 JVM 参数；

JVM 内存太小可能会影响导入导出的性能，甚至影响导入导出功能的稳定性。例如：Full GC 或者 GC Crash。
1.  编辑 `JAVA_OPTS` 选项所在的文件；
	1. *Linux 操作系统下*，编辑 `{ob-loader-dumper}/bin/` 目录下的 `obloader` 和 `obdumper` 脚本；
	2. *Windows 操作系统下*，编辑 `{ob-loader-dumper}/bin/windows/` 目录下的 `obloader.bat` 和 `obdumper.bat` 脚本；
2.  修改 JVM 参数；
	1. 建议您根据运行机器的实际规格，调整 JVM 内存为适当的值；

示例：运行机器可用内存为 16G，将 JVM 堆内存设置为 8G。
```shell
JAVA_OPTS="$JAVA_OPTS -server -Xms8G -Xmx8G -XX:MetaspaceSize=128M -XX:MaxMetaspaceSize=128M -Xss352K"
JAVA_OPTS="$JAVA_OPTS -XX:+UnlockExperimentalVMOptions -XX:+UseG1GC -Xnoclassgc -XX:+DisableExplicitGC

	# 其中，`-Xms8G -Xmx8G` 为设置的 JVM 参数。默认值：-Xms4G -Xmx4G。
```

#### 5 步骤 5：查看配置文件
导数工具配置文件包括连接配置文件（`session.config.json`）和运行日志文件（`log4j2.xml`）。

##### 5.1 连接配置文件
连接配置文件（`{ob-loader-dumper}/conf/session.config.json`）用于配置数据库连接参数。连接数据库时，导数工具会通过连接配置文件中的 JDBC 参数构建 JDBC URL，并在新建的连接下按顺序执行初始化 SQL 语句。

您可以在连接配置文件中修改 JDBC 参数和初始化 SQL 语句。连接配置的默认值适用于大多数场景，但在某些特殊情况下，您可能需要手动修改此类参数以适应不同的 OBServer 版本和 ETL 场景。连接配置文件详情请参见 [连接配置](https://www.oceanbase.com/docs/common-oceanbase-dumper-loader-1000000000775405)，；


##### 5.2 日志配置文件
日志配置文件（`{ob-loader-dumper}/conf/log4j2.xml`）用于查看日志输出路径/日志格式，以及[问题自查](https://www.oceanbase.com/docs/common-oceanbase-dumper-loader-1000000000775399)时调整日志级别。具体配置方法请参见 [如何自定义导出作业日志文件名](https://www.oceanbase.com/docs/common-oceanbase-dumper-loader-1000000000775417)，；

### 参考文档
1. [OceanBase 导数工具_3.3.2_OBLOADER 命令行选项](https://www.oceanbase.com/docs/enterprise-oceanbase-dumper-loader-cn-10000000000971197)，；
2. *下载工具*：[V4.3.0](https://www.oceanbase.com/docs/common-oceanbase-dumper-loader-1000000000775412)，；



