---
{"dg-publish":true,"permalink":"/Learning/大数据/HDFS 故障排除/"}
---


### 11.1 NameNode 故障处理

NameNode 进程挂了并且存储的数据也丢失了，如何恢复 NameNode

1.  kill -9 NameNode 进程

```
kill -9 19886
```

2.  删除 NameNode 存储的数据(/opt/hadoop-x.x.x/data/tmp/dfs/name)

```
rm -rf /opt/module/hadoop-x.x.x/data/dfs/name/*
```

3.  拷贝 SecondaryNameNode 中数据到原 NameNode 存储数据目录

```
scp -r xxx@xxx:/opt/module/hadoop-x.x.x/data/dfs/namesecondary/* ./name/
```

4.  重新启动 NameNode

```
hdfs --daemon start namenode
```

### 11.2 集群安全模式

1.  `安全模式`:文件系统只接受读数据请求，而不接受删除、修改等变更请求
2.  进入安全模式场景
    -   NameNode在加载镜像文件和编辑日志期间处于安全模式;
    -   NameNode在接收DataNode注册时，处于安全模式
3.  退出安全模式条件

```
dfs.namenode.safemode.min.datanodes:最小可用 datanode 数量，默认 0
dfs.namenode.safemode.threshold-pct:副本数达到最小要求的 block 占系统总 block 数的 百分比，默认 0.999f。(只允许丢一个块)
dfs.namenode.safemode.extension:稳定时间，默认值 30000 毫秒，即 30 秒 
```

4.  基本语法 集群处于安全模式，不能执行重要操作(写操作)。集群启动完成后，自动退出安全模式。

```
# 查看安全模式状态
[tpxcer@master01 hadoop-3.3.1]$ hdfs dfsadmin -safemode get

# 进入安全模式状态
bin/hdfs dfsadmin -safemode enter

# 离开安全模式状态
bin/hdfs dfsadmin -safemode leave

# 等待安全模式状态
bin/hdfs dfsadmin -safemode wait
```

5.  案例 磁盘修复

（1）模拟损坏，分别进入各datanode的`/hadoop-x.x.x/data/dfs/data/current/`中删除某 2 个块信息  
（2）重新启动集群 查看http://master01:9870/dfshealth.html#tab-overview ，会提示安全模式已经打开，块的数量没达到要求  
（3）离开安全模式

```
hdfs dfsadmin -safemode get
hdfs dfsadmin -safemode leave
```

6.  再查看查看http://master01:9870/dfshealth.html#tab-overview 根据提示处理