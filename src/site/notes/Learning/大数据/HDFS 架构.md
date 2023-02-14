---
{"dg-publish":true,"permalink":"/Learning/大数据/HDFS 架构/"}
---


![Pasted image 20221018231342.png|500](/img/user/Attachments/Pasted%20image%2020221018231342.png)
   
### 2.1 NameNode
1.  管理 HDFS 的名称空间
2.  配置副本策略
3.  管理数据块（Block）映射信息

### 2.2 Secondary NameNode
并非 NameNode 的热备。当 NameNode 挂掉的时候，它并不能马上替换 NameNode 提供服务。
1.  辅助 NameNode, 分担其工作量，比如定期合并 [[Learning/大数据/Fsimage和Edits\|Fsimage和Edits]]，并推送给 NameNode
2.  在紧急情况下，可辅助恢复 NameNode

### 2.3 Client
1.  文件切分。文件上传 HDFS 的时候，Client 将文件切分成一个一个的 Block，然后进行上传;
2.  与 NameNode 交互，获取文件的位置信息;
3.  与 DataNode 交互，读取或者写入数据;
4.  Client 提供一些命令来管理 HDFS，比如 NameNode 格式化;
5.  Client 可以通过一些命令来访问 HDFS，比如对 HDFS 增删查改操作;

### 2.4 DataNode
就是 Slave。NameNode 下达命令，DataNode 执行实际的操作。
1.  存储实际的数据块;
2.  执行数据块的读/写操作。

### 2.5 HDFS 写数据流程
1.  Client：向 NameNode 请求上传文件 xx
2.  NameNode：响应是否可以上传文件（会检查权限，目录结构等）
3.  Client：请求上传第一个 Block，让 NameNode 返回 DataNode
4.  NameNode：返回节点（优先本地节点，比如在 DN1 上传数据，就优先放 DN1，NameNode 还得考虑负载均衡等）
5.  Client：请求与 DataNode 建立 Block 传输通道
6.  DataNode：应答
7.  Client：传输数据
8.  Client：告诉 Namenode 数据传输完成

### 2.6 HDFS 读数据流程
1.  Client：请求下载文件 xx
2.  NameNode：返回目标文件的元数据
3.  Client：请求读取 DataNode 数据（多个 DataNode）
4.  DataNode：传输数据

### 2.7 NameNode 工作机制
**NameNode**
1.  NameNode：通电后先加载 edits_inprogress_xx 和 fsimage_xx 到内存
2.  Client：发送元数据的增删改请求 xxx
3.  NameNode：在 edits_inprogress 中记录要干什么，然后再更改内存
4.  NameNode：内存数据增删改

**Secondary NameNode**
1.  Secondary NameNode：问 NameNode 是否要 CheckPoint （触发条件，定时间到（默认一小时）或 Edits 中数据满了）
2.  Secondary NameNode：可以 [[Learning/大数据/CheckPoint\|CheckPoint]] 以后请求执行
3.  NameNode：生成新的 edits_inprogress, 如果 CheckPoint 过程中有新的访问，记录会往新的 edits_inprogress 文件写。原来的 edits_inprogress 变为 edits_xx
4.  Secondary NameNode：把 NameNode 的 fsimage 和 edits_xx 都拷贝过来
5.  Secondary NameNode：将拷贝的文件加载到内存，然后执行合并操作。
6.  Secondary NameNode：生成一个新文件 `fsimage.chkpoint`
7.  Secondary NameNode：把新文件 `fsimage.chkpoint` 拷贝到 NameNode
8.  NameNode：把 `fsimage.chkpoint` 修改名称覆盖 fsimage

### 2.8 Fsimage 和 Edits 概念
[[Learning/大数据/Fsimage和Edits\|Fsimage和Edits]]

### 2.9 CheckPoint 时间设置
1.  通常情况下，SecondaryNameNode 每隔一小时执行一次。
hdfs-default. xml

```
<property>
   <name>dfs.namenode.checkpoint.period</name>
   <value>3600s</value>
</property>
```

2.  一分钟检查一次操作次数，当操作次数达到 1 百万时，SecondaryNameNode 执行一次。

```
   <property>
      <name>dfs.namenode.checkpoint.txns</name>
      <value>1000000</value> <description>操作动作次数</description> 
   </property>
   <property>
      <name>dfs.namenode.checkpoint.check.period</name>
      <value>60s</value>
       <description> 1 分钟检查一次操作次数</description>
   </property>
```

### 2.10 DataNode 工作机制
1.  一个数据块在 DataNode 上以文件形式存储在磁盘上，包括两个文件，一个是数据本身，一个是元数据包括数据块的长度，块数据的校验和，以及时间戳。
2.  DataNode 启动后向 NameNode 注册，通过后，周期性 (6 小时) 的向 NameNode 上报所有的块信息。 DN 向 NN 汇报当前解读信息的时间间隔，默认 6 小时;
    
    ```
    <property>
         <name>dfs.blockreport.intervalMsec</name>
         <value>21600000</value>
         <description>Determines block reporting interval in milliseconds.</description>
    </property>
    ```
DN 扫描自己节点块信息列表的时间，默认 6 小时

```
 <property>
     <name>dfs.datanode.directoryscan.interval</name>
     <value>21600s</value>
 </property>
```
1.  心跳是每 3 秒一次，心跳返回结果带有 NameNode 给该 DataNode 的命令如复制块数据到另一台机器，或删除某个数据块。如果超过 10 分钟没有收到某个 DataNode 的心跳，则认为该节点不可用。
2.  集群运行中可以安全加入和退出一些机器。
### 2.11 DataNode 数据完整性
1.  当 DataNode 读取 Block 的时候，它会计算 CheckSum。
2.  如果计算后的 CheckSum，与 Block 创建时值不一样，说明 Block 已经损坏。
3.  Client 读取其他 DataNode 上的 Block。
4.  常见的校验算法 crc (32)，md5 (128)，sha1 (160)
5.  DataNode 在其文件创建后周期验证 CheckSum。

### 2.12 DataNode 掉线时间参数
1.  DataNode 进程死亡或者网络故障造成 DataNode 无法与 NameNode 通信
2.  NameNode 不会立即把该节点判定为死亡，要经过一段时间，这段时间暂称作超时时长。
3.  HDFS 默认的超时时长为 10 分钟+30 秒。
4.  如果定义超时时间为 TimeOut，则超时时长的计算公式为: TimeOut = 2 * dfs. namenode. heartbeat. recheck-interval + 10 * dfs. heartbeat. interval。而默认的 dfs. namenode. heartbeat. recheck-interval 大小为 5 分钟，dfs. heartbeat. interval 默认为 3 秒。

需要注意的是 hdfs-site. xml 配置文件中的 heartbeat. recheck. interval 的单位为毫秒， dfs. heartbeat. interval 的单位为秒。

```
<property>
   <name>dfs.namenode.heartbeat.recheck-interval</name>
   <value>300000</value>
</property>
<property>
   <name>dfs.heartbeat.interval</name>
   <value>3</value>
</property>
```

