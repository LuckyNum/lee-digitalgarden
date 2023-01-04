---
{"dg-publish":true,"permalink":"/Learning/大数据/HDFS 常用 Shell 命令/"}
---


### 3.1 从本地加载文件到 HDFS
```
# 二选一执行即可
hadoop fs -put  [localsrc] [dst] 
hadoop fs -copyFromLocal [localsrc] [dst] 
```

### 3.2 从 HDFS 导出文件到本地

```
# 二选一执行即可
hadoop fs -get  [dst] [localsrc] 
hadoop fs -copyToLocal [dst] [localsrc] 
```

### 3.3 统计当前目录下各文件大小

-   默认单位字节
-   -s : 显示所有文件大小总和，
-   -h : 将以更友好的方式显示文件大小（例如 64.0m 而不是 67108864）

```
hadoop fs -du  <path>
# 显示某个目录的总大小
hadoop fs -du -h -s  /user/hive/warehouse/name
```

### 3.4 合并下载多个文件

-   -nl 在每个文件的末尾添加换行符（LF）
-   -skip-empty-file 跳过空文件

```
hadoop fs -getmerge
# 示例 将HDFS上的hbase-policy.xml和hbase-site.xml文件合并后下载到本地的/usr/test.xml
hadoop fs -getmerge -nl  /test/hbase-policy.xml /test/hbase-site.xml /usr/test.xml
```

### 3.5 更改文件复制因子

```
hadoop fs -setrep [-R] [-w] <numReplicas> <path>
```

-   更改文件的复制因子。如果 path 是目录，则更改其下所有文件的复制因子
-   -w : 请求命令是否等待复制完成

```
# 示例
hadoop fs -setrep -w 3 /user/hadoop/dir1
```

### 3.6 权限控制

```
# 权限控制和Linux上使用方式一致
# 变更文件或目录的所属群组。 用户必须是文件的所有者或超级用户。
hadoop fs -chgrp [-R] GROUP URI [URI ...]
# 修改文件或目录的访问权限  用户必须是文件的所有者或超级用户。
hadoop fs -chmod [-R] <MODE[,MODE]... | OCTALMODE> URI [URI ...]
# 修改文件的拥有者  用户必须是超级用户。
hadoop fs -chown [-R] [OWNER][:[GROUP]] URI [URI ]
```

创建 hive 目录和权限

```
hadoop fs –mkdir /tmp
hadoop fs –chmod a+w /tmp
hadoop fs –mkdir /user/hive/warehouse
hadoop fs –chmod a+w /user/hive/warehouse
```

### 3.7 追加一个文件到已经存在的文件末尾
```
hadoop fs -appendToFile aa.txt bb.txt
```

> 本篇文章只是简单阐述一下 [[Learning/大数据/HDFS\|HDFS]] 中常用命令, 在实际开发中可使用 bin/hadoop fs 查看命令详情

```shell
使用HDFS基本语法: bin/hadoop fs	OR bin/hdfs dfs
	注:为帮助快速理解并使用本文中使用T表示target
基本命令
	1.启动hadoop集群
		HDFS相关组件: sbin/start-dfs.sh 
		YARN相关组件: sbin/start-yarn.sh
	2.显示目录信息
		-ls
		hadoop fs -ls $pdir
	3.在HDFS上创建目录
		-mkdir
		hadoop fs -mkdir -p $pdir/$fname
	4.从本地剪贴文件[目录]到HDFS
		-moveFromLocal
		hadoop fs -moveFromLocal $pdir/$fname $Tpdir
	5.追加文件到已存在文件的末尾
		-appendTOFile
		hadoop fs -appendToFile $pdir/$fname $Tpdir/$fname
	6.显示文件内容
		-cat
		hadoop fs -cat $pdir/$fname
	7.修改文件[目录]所属权限
		-chgrp -chmod -chown
		hadoop fs -chown $pdir/$fname
	8.HDFS拷贝文件[目录]
		-cp
		hadoop fs -cp $pdir/$fname $Tpdir/$fname
	9.HDFS移动文件
		-mv
		hadoop fs -nv $pdir/$fname $Tpdir/$fname
	10.从HDFS上下载文件[目录]到本地
		-get
		hadoop fs -get $pdir/$fname $Tpdir
	11.本地上传文件到HDFS
		-put
		hadoop fs -put $pdir/$fname $Tpdir
	12.合并下载目录下多个文件
		-getmerge
		hadoop fs -getmerge $pdir/$fanme $Tpdir/$newfname
	13.显示一个文件的末尾
		-tail
		hadoop fs -taile $pdir/$fname
	14.删除文件或文件夹
		-rm
		hadoop fs -rm [-r] $pdir/$fname
	15.统计文件夹的大小信息
		-du
		hadoop fs -du -s -h $pdir
		选项说明: -h 人们理解的方式查看信息 -s 该目录下所有文件合并在一起占用的空间大小
	16.设置HDFS文件的副本数量
		-setrep
		hadoop fs -setrep n $pdir/$fname
		注: 这里设置的副本数只是记录在NameNode的元数据中,实际是否会有这么多副本取决于DataNode的数量.
```