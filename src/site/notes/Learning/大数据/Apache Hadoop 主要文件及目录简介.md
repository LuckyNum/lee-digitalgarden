---
{"dg-publish":true,"permalink":"/Learning/大数据/Apache Hadoop 主要文件及目录简介/"}
---


## 1. hadoop目录概述
hadoop的解压目录下的主要文件如下图所示：
![Pasted image 20221017223826.png|800](/img/user/Attachments/Pasted%20image%2020221017223826.png)
其中：
/bin 目录存放对Hadoop相关服务（HDFS, YARN）进行操作的脚本；
/etc 目录存放Hadoop的配置文件
/lib 目录存放Hadoop的本地库（对数据进行压缩解压缩功能）
/sbin 目录存放启动或停止Hadoop相关服务的脚本
/share 目录存放Hadoop的依赖jar包、文档、和官方案例
下文将对常用的几个目录进行进一步的介绍。
## 2. bin、sbin目录
bin是一般用户都可以使用的命令，如文件的增删改；sbin 目录存放root用户的管理类程序，如节点的启停。

### 2.1 bin
/bin 目录下的主要命令如下：
![Pasted image 20221017223845.png|800](/img/user/Attachments/Pasted%20image%2020221017223845.png)
- [[Learning/大数据/HDFS 常用 Shell 命令\|HDFS 常用 Shell 命令]]

### 2.2 sbin
/sbin 目录下的主要命令如下：
![Pasted image 20221017223937.png|800](/img/user/Attachments/Pasted%20image%2020221017223937.png)
sbin 目录下操作命令的主要功能是对集群进行配置和启停，具体的应用文末会给出相应的实操链接。

## 3. ect/hadoop 目录
ect/hadoop 目录下存放集群的配置文件，如下：
![Pasted image 20221017223958.png|800](/img/user/Attachments/Pasted%20image%2020221017223958.png)
上述配置文件配置了HDFS、MapReduce、Yarn的运行参数，配置文件结合sbin目录下的命令对可以集群进行综合的管理。