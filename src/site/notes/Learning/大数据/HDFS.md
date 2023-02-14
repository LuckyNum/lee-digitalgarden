---
{"dg-publish":true,"permalink":"/Learning/大数据/HDFS/"}
---


## 一、HDFS 优缺点
### 1.1 HDFS优点
1.  高容错性
    - 数据自动保存多个副本。它通过增加副本的形式，提高容错性。
    - 某一个副本丢失以后，它可以自动恢复。
2.  适合处理大数据
   - 数据规模:能够处理数据规模达到GB、TB、甚至PB级别的数据;
   - 文件规模:能够处理百万规模以上的文件数量，数量相当之大。
3.  可构建在廉价机器上，通过多副本机制，提高可靠性。
### 1.2 HDFS缺点
1.  不适合低延时数据访问，比如毫秒级的存储数据，是做不到的。
2.  无法高效的对大量小文件进行存储。
    - 存储大量小文件的话，它会占用NameNode大量的内存来存储文件目录和块信息。这样是不可取的，因为NameNode的内存总是有限的;
    - 小文件存储的寻址时间会超过读取时间，它违反了HDFS的设计目标。
3.  不支持并发写入、文件随机修改。
    - 一个文件只能有一个写，不允许多个线程同时写;
    - 仅支持数据 append (追加)，不支持文件的随机修改。

## 二、HDFS 架构
[[Learning/大数据/HDFS 架构\|HDFS 架构]]

## 三、HDFS命令行
### 3.1 格式
![Pasted image 20221022105046.png|500](/img/user/Attachments/Pasted%20image%2020221022105046.png)

### 3.2 常用 Shell 命令
[[Learning/大数据/HDFS 常用 Shell 命令\|HDFS 常用 Shell 命令]]

## 四、HDFS Java API
[[Learning/大数据/HDFS Java API\|HDFS Java API]]

## 五、HDFS 核心参数
[[Learning/大数据/HDFS 核心参数\|HDFS 核心参数]]

## 六、HDFS 群集压测
[[Learning/大数据/HDFS 群集压测\|HDFS 群集压测]]

## 七、HDFS 集群扩容及缩容
[[Learning/大数据/HDFS 集群扩容及缩容\|HDFS 集群扩容及缩容]]

## 八、HDFS 存储优化
[[Learning/大数据/HDFS 存储优化\|HDFS 存储优化]]

## 九、异构存储 (冷热数据分离)
[[Learning/大数据/HDFS 异构存储(冷热数据分离)\|HDFS 异构存储(冷热数据分离)]]

## 十、HDFS 故障排除
[[Learning/大数据/HDFS 故障排除\|HDFS 故障排除]]