---
{"dg-publish":true,"permalink":"/Learning/大数据/HDFS 异构存储(冷热数据分离)/"}
---


> 异构存储主要解决，不同的数据，存储在不同类型的硬盘中，达到最佳性能的问题。

### 9.1 存储类型和存储策略

1.  关于存储类型
-   RAM_DISK:(内存镜像文件系统)
-   SSD:(SSD固态硬盘)
-   DISK:(普通磁盘，在HDFS中，如果没有主动声明数据目录存储类型默认都是DISK)
-   ARCHIVE:(没有特指哪种存储介质，主要的指的是计算能力比较弱而存储密度比较高的存储介质，用来解决数据量的 容量扩增的问题，一般用于归档)

2.  关于存储策略说明: 从 Lazy_Persist 到 Cold，分别代表了设备的访问速度从快到慢

| 策略 ID | 策略名称         | 副本分布                | 说明                             |
|------|--------------|---------------------|--------------------------------|
| 15   | Lazy_Persist | RAM_DISK:1，DISK:n-1 | 一个副本保存在内存RAM_DISK中，其余副本保存在磁盘中。 |
| 12   | All_SSD      | SSD:n               | 所有副本都保存在SSD中。                  |
| 10   | One_SSD      | SSD:1，DISK:n-1      | 一个副本保存在SSD中，其余副本保存在磁盘中。        |
| 7    | Hot(default) | DISK:n              | Hot:所有副本保存在磁盘中，这也是默认的存储策略。     |
| 5    | Warm         | DSIK:1，ARCHIVE:n-1  | 一个副本保存在磁盘上，其余副本保存在归档存储上。       |
| 2    | Cold         | ARCHIVE: n           | 所有副本都保存在归档存储上。                 |

### 9.2 异构存储 Shell 操作

1.  查看当前有哪些存储策略可以用

```
[tpxcer@master01 ~]$ hdfs storagepolicies -listPolicies
```

2.  为指定路径(数据存储目录)设置指定的存储策略

```
hdfs storagepolicies -setStoragePolicy -path xxx -policy xxx
```

3.  获取指定路径(数据存储目录或文件)的存储策略

```
hdfs storagepolicies -getStoragePolicy -path xxx
```

4.  取消存储策略;执行改命令之后该目录或者文件，以其上级的目录为准，如果是根目录，那么就是 HOT

```
hdfs storagepolicies -unsetStoragePolicy -path xxx
```

5.  查看文件块的分布

```
bin/hdfs fsck xxx -files -blocks -locations
```

6.  查看集群节点

```
hadoop dfsadmin -report
```

### 9.3 测试环境准备& 实践
TODO