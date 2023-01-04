---
{"dg-publish":true,"permalink":"/Learning/大数据/HDFS 核心参数/"}
---


### 5.1 NameNode 内存生产配置

1.  NameNode 内存计算

每个文件块大概占用 150byte，一台服务器 128G 内存为例，能存储多少文件块呢?

```
128 * 1024 * 1024 * 1024 / 150Byte ≈ 9.1亿
G     MB     KB     Byte
```

2.  Hadoop2. x 系列，配置 NameNode 内存

NameNode 内存默认 2000m，如果服务器内存 4G，NameNode 内存可以配置 3g。在hadoop-env.sh 文件中配置如下。

```
HADOOP_NAMENODE_OPTS=-Xmx3072m
```

3.  Hadoop3. x 系列，配置 NameNode 内存

hadoop-env.sh 中描述 Hadoop 的内存是动态分配的

具体修改:hadoop-env.sh

```
export HDFS_NAMENODE_OPTS="-Dhadoop.security.logger=INFO,RFAS - Xmx1024m"
export HDFS_DATANODE_OPTS="-Dhadoop.security.logger=ERROR,RFAS -Xmx1024m"
```

4.  查看 NameNode 占用内存

```
[tpxcer@master01 root]$ jps
75153 NameNode
75377 DataNode
75741 NodeManager
76015 JobHistoryServer
45775 Jps
[tpxcer@master01 root]$  jmap -heap 75153
Heap Configuration:
   MaxHeapSize              = 2046820352 (1952.0MB)
[tpxcer@master01 root]$  jmap -heap 75377
Heap Configuration:
   MaxHeapSize              = 2046820352 (1952.0MB)
```

上面 NameNode 和 DataNode 占用内存都是自动分配的，且相等。不是很合理。经验参考: https://docs.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_hardware_requirements.html

### 5.2 NameNode 心跳并发配置

`hdfs-site.xml`

```
The number of Namenode RPC server threads that listen to requests from clients. If dfs.namenode.servicerpc-address is not configured then Namenode RPC server threads listen to requests from all nodes.
NameNode 有一个工作线程池，用来处理不同 DataNode 的并发心跳以及客户端并发 的元数据操作。
对于大集群或者有大量客户端的集群来说，通常需要增大该参数。默认值是 10。 <property>
<name>dfs.namenode.handler.count</name>
   <value>21</value>
</property>
```

企业经验: dfs. namenode. handler. count= $20*\log(cluster size)$，比如集群规模 (DataNode 台 𝑒 数) 为 3 台时，此参数设置为 21。可通过简单的 python 代码计算该值，代码如下。

### 5.3 开启回收站配置
开启回收站功能，可以将删除的文件在不超时的情况下，恢复原数据，起到防止误删除、备份等作用。

1.  开启回收站功能参数说明

-   默认值 fs.trash.interval = 0，0 表示禁用回收站;其他值表示设置文件的存活时间。
-   默认值 fs.trash.checkpoint.interval = 0，检查回收站的间隔时间。如果该值为 0，则该值设置和 fs.trash.interval 的参数值相等。
-   要求 fs.trash.checkpoint.interval <= fs.trash.interval。

2.  启用回收站

修改 core-site.xml，配置垃圾回收时间为 1 分钟。
```
<property> 
   <name>fs.trash.interval</name> 
   <value>1</value>
</property>
```

3.  查看回收站

```
/user/xxx/.Trash/...
```

4.  通过网页上直接删除的文件也不会走回收站。
5.  通过程序删除的文件不会经过回收站，需要调用 moveToTrash()才进入回收站

```
Trash trash = New Trash(conf);
trash.moveToTrash(path);
```
1. 只有在命令行利用 hadoop fs -rm 命令删除的文件才会走回收站。

### 5.4 NameNode 多目录配置
在 hdfs-site. xml 文件中添加如下内容

```
<property> 
    <name>dfs.namenode.name.dir</name>
    <value>file://${hadoop.tmp.dir}/dfs/name1,file://${hadoop.tmp.dir}/dfs/name2</value>
</property>
```

检查 name1 和 name2 里面的内容，发现一模一样。

### 5.4 DataNode 多目录配置
```
<property> 
    <name>dfs.datanode.data.dir</name>
    <value>file://${hadoop.tmp.dir}/dfs/data1,file://${hadoop.tmp. dir}/dfs/data2</value>
</property>
```

### 5.5 集群数据均衡之磁盘间数据均衡
生产环境，由于硬盘空间不足，往往需要增加一块硬盘。刚加载的硬盘没有数据时，可以执行磁盘数据均衡命令。(Hadoop3. x 新特性)

1.  生成均衡计划(我们只有一块磁盘，不会生成计划)

```
hdfs diskbalancer -plan hadoop103
```

2.  执行均衡计划

```
hdfs diskbalancer -execute hadoop103.plan.json
```

3.  查看当前均衡任务的执行情况

```
hdfs diskbalancer -query hadoop103
```

4.  取消均衡任务

```
hdfs diskbalancer -cancel hadoop103.plan.json
```



