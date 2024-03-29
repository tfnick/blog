---
title : "一个项目的诞生(三)：RocksDB的价值"
description : "一个项目的诞生(三)：RocksDB的价值"
date : "2021-03-19"
aliases : ["折腾"]
author : "飞天"
toc : true
---




## 概念

RocksDB是一个本地嵌入式的KV存储引擎，其键值均允许使用二进制流。由Facebook基于levelDB开发， 提供向后兼容的levelDB API。

RocksDB依靠大量灵活的配置，使之能针对不同的生产环境进行调优，包括直接使用内存，使用Flash（闪存），使用硬盘或者HDFS。支持使用不同的压缩算法，并且有一套完整的工具供生产和调试使用。

## 场景与价值

传统的数据访问都是RPC，但是这样的话访问速度会很慢，不适用于面向用户的实时访问的场景。随着fast storage的流行，越来越多的应用可以通过在flash中管理数据并快速直接的访问数据。这些应用就需要使用到一种嵌入式的database。

首先，使用嵌入式的database的原因有很多。当数据请求频繁访问内存或者fast storage时，`网路延时`会增加响应时间，比如：访问数据中心网络耗时可能就耗费50ms，跟访问数据的耗时一样多，甚至更多。这意味着，通过RPC访问数据有可能是本地直接访问耗时的两倍。

另外，机器的core数越来越多，storage-IOPS的访问频率也达到了每秒百万次，传统数据库的锁竞争和context 切换会成为提高storage-IOPS的瓶颈。所以需要一种容易扩展和针对未来硬件趋势可以定制化的database，RocksDB就是一种选择。

很多情况下，可以简单把RocksDB看成`本地的Redis`，访问RocksDb会比网路访问Redis来的更快，特别是访问的数据量过Millions级别。因为Redis除了有网络开销，还有数据网络传输的开销。

- 为需要存储TB级别数据到本地FLASH或者RAM的应用服务器设计（数据量大）
- 针对存储在高速设备的中小键值进行优化——你可以存储在flash或者直接存储在内存（数据量小）
- 性能碎CPU数量线性提升，对多核系统友好（避免争用与上下文切换，提升多核效率）

## RocksDB常见身影

- Kafka Streams 流式处理
- Flink 流批处理

## 参考

[RocksDB中文](