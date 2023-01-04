---
{"dg-publish":true,"permalink":"/Learning/大数据/HDFS 存储优化/"}
---


### 8.1 纠删码
HDFS 默认情况下，一个文件有 3 个副本，这样提高了数据的可靠性，但也带来了 2 倍 的冗余开销。Hadoop3.x 引入了纠删码，采用计算的方式，可以节省约 50%左右的存储空间。

1.  纠删码操作相关的命令

```
[tpxcer@master01 ~]$ hdfs ec
```

2.  查看当前支持的纠删码策略

```
[tpxcer@master01 ~]$ hdfs ec -listPolicies
Erasure Coding Policies:
ErasureCodingPolicy=[Name=RS-10-4-1024k, Schema=[ECSchema=[Codec=rs, numDataUnits=10, numParityUnits=4]], CellSize=1048576, Id=5], State=DISABLED
ErasureCodingPolicy=[Name=RS-3-2-1024k, Schema=[ECSchema=[Codec=rs, numDataUnits=3, numParityUnits=2]], CellSize=1048576, Id=2], State=DISABLED
ErasureCodingPolicy=[Name=RS-6-3-1024k, Schema=[ECSchema=[Codec=rs, numDataUnits=6, numParityUnits=3]], CellSize=1048576, Id=1], State=ENABLED
ErasureCodingPolicy=[Name=RS-LEGACY-6-3-1024k, Schema=[ECSchema=[Codec=rs-legacy, numDataUnits=6, numParityUnits=3]], CellSize=1048576, Id=3], State=DISABLED
ErasureCodingPolicy=[Name=XOR-2-1-1024k, Schema=[ECSchema=[Codec=xor, numDataUnits=2, numParityUnits=1]], CellSize=1048576, Id=4], State=DISABLED
```

3.  纠删码策略解释

RS-3-2-1024k:使用 RS 编码，每 3 个数据单元，生成 2 个校验单元，共 5 个单元，也就是说:这 5 个单元中，只要有任意的 3 个单元存在(不管是数据单元还是校验单元，只要总数=3)，就可以得到原始数据。每个单元的大小是 1024k=1024*1024=1048576。

RS-10-4-1024k:使用 RS 编码，每 10 个数据单元(cell)，生成 4 个校验单元，共 14 个单元，也就是说:这 14 个单元中，只要有任意的 10 个单元存在(不管是数据单元还是校 验单元，只要总数=10)，就可以得到原始数据。每个单元的大小是1024k=1024*1024=1048576。

RS-6-3-1024k:使用 RS 编码，每 6 个数据单元，生成 3 个校验单元，共 9 个单元，也 就是说:这 9 个单元中，只要有任意的 6 个单元存在(不管是数据单元还是校验单元，只要 总数=6)，就可以得到原始数据。每个单元的大小是 1024k=1024*1024=1048576。

RS-LEGACY-6-3-1024k:策略和上面的 RS-6-3-1024k 一样，只是编码的算法用的是 rs-legacy。

XOR-2-1-1024k:使用 XOR 编码(速度比 RS 编码快)，每 2 个数据单元，生成 1 个校 验单元，共 3 个单元，也就是说:这 3 个单元中，只要有任意的 2 个单元存在(不管是数据 单元还是校验单元，只要总数= 2)，就可以得到原始数据。每个单元的大小是1024k=1024*1024=1048576。

### 8.2 纠删码案例实操

纠删码策略是给具体一个路径设置。所有往此路径下存储的文件，都会执行此策略。 默认只开启对 RS-6-3-1024k 策略的支持，如要使用别的策略需要提前启用。

需求:将/input 目录设置为 RS-3-2-1024k 策略

1.  开启对 RS-3-2-1024k 策略的支持

```
hdfs ec -enablePolicy -policy RS-3-2-1024k 
```

2.  在 HDFS 创建目录，并设置 RS-3-2-1024k 策略

```
hdfs dfs -mkdir /input
hdfs ec -setPolicy -path /input -policy RS-3-2-1024k
```

