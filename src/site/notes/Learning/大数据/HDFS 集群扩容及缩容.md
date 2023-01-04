---
{"dg-publish":true,"permalink":"/Learning/大数据/HDFS 集群扩容及缩容/"}
---


### 7.1 添加白名单
在白名单中的主机 ip 都可以随意访问群集数据

1.  在 `/hadoop-3.3.1/etc/hadoop` 目录下分别创建 `whitelist` 和 `blacklist` 文件
2.  在白名单中添加主机名

```
master01
slave01
slave02
```

3.  在 `hdfs-site.xml` 配置文件中增加 dfs. hosts 配置参数

```
<!-- 白名单 --> 
<property>
    <name>dfs.hosts</name>
    <value>/hadoop-3.3.1/etc/hadoop/whitelist</value> 
</property>
<!-- 黑名单 --> 
<property>
    <name>dfs.hosts.exclude</name>
    <value>/hadoop-3.3.1/etc/hadoop/blacklist</value>
</property>
```

4.  分发配置文件
5.  第一次添加白名单必须重启集群，不是第一次，只需要刷新 NameNode 节点即可

```
# 刷新节点
hdfs dfsadmin -refreshNodes
```

### 7.2 服役新服务器

1.  环境准备

-   克隆一台Hadoop主机
-   修改IP地址和主机名称
-   拷贝配置
-   删除 克隆 Hadoop 的历史数据，data 和 log 数据
-   配置ssh 无密登录

1.  直接启动 DataNode，即可关联到集群
2.  在白名单中增加新服役的服务器

### 7.3 服务器间数据均衡

1.  开启数据均衡命令

```
sbin/start-balancer.sh - threshold 10
```

对于参数 10，代表的是集群中各个节点的磁盘空间利用率相差不超过 10%，可根据实际情况进行调整。

2.  停止数据均衡命令

```
sbin/stop-balancer.sh
```

> 由于 HDFS 需要启动单独的 Rebalance Server 来执行 Rebalance 操作，所以尽量不要在 NameNode 上执行 start-balancer. sh，而是找一台比较空闲的机器。

### 7.4 黑名单退役服务器

1.  第一次添加黑名单必须重启集群，不是第一次，只需要刷新 NameNode 节点即可

```
hdfs dfsadmin -refreshNodes Refresh nodes successful
```

2.  检查 Web 浏览器，退役节点的状态为 decommission in progress(退役中)，说明数据 节点正在复制块到其他节点
3.  等待退役节点状态为 decommissioned(所有块已经复制完成)，停止该节点及节点资源管理器。

```
hdfs --daemon stop datanode
yarn --daemon stop nodemanager
```

> 注意: 如果副本数是 3，服役的节点小于等于 3，是不能退役成功的，需要修改副本数后才能退役

4.  如果数据不均衡，可以用命令实现集群的再平衡

```
sbin/start-balancer.sh - threshold 10
```
