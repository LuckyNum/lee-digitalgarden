---
{"dg-publish":true,"permalink":"/Learning/大数据/HDFS 群集压测/"}
---


### 6.1 测试 HDFS 写性能
1.  测试内容: 向 HDFS 集群写 10 个 128M 的文件

```
[tpxcer@master01 root]$ hadoop jar /opt/hadoop-3.3.1/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-3.3.1-tests.jar TestDFSIO -write -nrFiles 15 -fileSize 128MB
2021-09-03 17:52:48,167 INFO fs.TestDFSIO: ----- TestDFSIO ----- : write
2021-09-03 17:52:48,167 INFO fs.TestDFSIO:             Date & time: Fri Sep 03 17:52:48 CST 2021
2021-09-03 17:52:48,167 INFO fs.TestDFSIO:         Number of files: 15
2021-09-03 17:52:48,167 INFO fs.TestDFSIO:  Total MBytes processed: 1920
2021-09-03 17:52:48,167 INFO fs.TestDFSIO:       Throughput mb/sec: 1.2
2021-09-03 17:52:48,168 INFO fs.TestDFSIO:  Average IO rate mb/sec: 2.7
2021-09-03 17:52:48,168 INFO fs.TestDFSIO:   IO rate std deviation: 5.7
2021-09-03 17:52:48,168 INFO fs.TestDFSIO:      Test exec time sec: 161.51
```

> nrFiles n 为生成 mapTask 的数量，生产环境一般可通过 hadoop1 03:80 88 查看 CPU 核数，设置为 (CPU 核数 - 1)

-   Numberoffiles: 生成 mapTask 数量，一般是集群中 (CPU 核数-1)
-   TotalMBytesprocessed:单个map处理的文件大小
-   Throughputmb/sec:单个mapTak的吞吐量  
    计算方式:处理的总文件大小/每一个 mapTask 写数据的时间累加 集群整体吞吐量:生成 mapTask 数量*单个 mapTak 的吞吐量
-   Average IO rate mb/sec::平均 mapTak 的吞吐量 计算方式:每个 mapTask 处理文件大小/每一个 mapTask 写数据的时间全部相加除以 task 数量
-   IO rate std deviation: 方差、反映各个 mapTask 处理的差值，越小越均衡

2.  测试结果分析

一共参与测试的文件: 15 个文件 * 3 个副本 = 45 个压测后的速度: 1.2 实测速度: 1.2M/s * 45 个文件 ≈ 54M/s

### 6.2 测试 HDFS 读性能
测试内容: 读取 HDFS 集群 10 个 128M 的文件

```
[tpxcer@master01 root]$ hadoop jar /opt/hadoop-3.3.1/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-3.3.1-tests.jar TestDFSIO -read -nrFiles 8 -fileSize 128MB
2021-09-03 18:22:56,694 INFO fs.TestDFSIO: ----- TestDFSIO ----- : read
2021-09-03 18:22:56,695 INFO fs.TestDFSIO:             Date & time: Fri Sep 03 18:22:56 CST 2021
2021-09-03 18:22:56,695 INFO fs.TestDFSIO:         Number of files: 8
2021-09-03 18:22:56,695 INFO fs.TestDFSIO:  Total MBytes processed: 1024
2021-09-03 18:22:56,695 INFO fs.TestDFSIO:       Throughput mb/sec: 130.05
2021-09-03 18:22:56,695 INFO fs.TestDFSIO:  Average IO rate mb/sec: 131.33
2021-09-03 18:22:56,695 INFO fs.TestDFSIO:   IO rate std deviation: 12.87
2021-09-03 18:22:56,695 INFO fs.TestDFSIO:      Test exec time sec: 43.76
2021-09-03 18:22:56,695 INFO fs.TestDFSIO:
```

### 6.3 删除测试生成数据
```
[tpxcer@master01 root]$ hadoop jar /opt/hadoop-3.3.1/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-3.3.1-tests.jar TestDFSIO -clean
```

