---
{"dg-publish":true,"permalink":"/Learning/大数据/Hadoop/"}
---


## 简介
Hadoop形成一套完善的大数据生态系统

## 发行版
- [[Learning/大数据/Apache Hadoop\|Apache Hadoop]]：官方版本，开源
- [[Learning/大数据/Cloudera Hadoop(CDH)\|Cloudera Hadoop(CDH)]]：商业版本，对官方版本做了优化，提供收费技术支持（基本功能不收费），提供**界面操作**，方便**集群管理**
- [[Learning/大数据/HortonWorks(HDP)\|HortonWorks(HDP)]]：开源，提供界面操作，方便运维管理

> [!info]- 建议
> 工作中，使用[[Learning/大数据/Cloudera Hadoop(CDH)\|Cloudera Hadoop(CDH)]]或[[Learning/大数据/HortonWorks(HDP)\|HortonWorks(HDP)]]

## 版本历史
### hadoop1.x
- MapReduce
	- 分布式计算
- HDFS
	- 分布式存储
### hadoop2.x
- MapReduce
	- 分布式计算
- HDFS
	- 分布式存储
- YARN
	- 资源管理平台
- Others
### hadoop3.x
- MapReduce
	- 分布式计算
- HDFS
	- 分布式存储
- YARN
	- 资源管理平台
- Others
- 细节优化
	- Java支持8及以上
	- HDFS支持[[纠删码\|纠删码]]
	- HDFS支持多NameNode
	- MR任务级本地优化
	- 多重服务默认端口变更

## 核心组件
- [[Learning/大数据/HDFS\|HDFS]]负责海量数据存储，[[分布式存储\|分布式存储]]
- [[Inbox/MapReduce\|MapReduce]]是计算模型，负责海量数据的[[分布式计算\|分布式计算]]
- [[Learning/大数据/YARN\|YARN]]主要负责集群资源的管理和调度
