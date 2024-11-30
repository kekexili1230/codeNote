# flink笔记

## flink 概述

flink是一个开源的流处理框架，具有如下特点：

- 分布式

- 高性能

- 高可用

- 准确

- 流式优先

- 容错

- 可伸缩


## flink基本组件

- DataSource：表示数据源组件

- Transformation：算子，对数据进行处理

- DataSink：输出组件，将结果输出到其他介质中

## flink的批处理和流处理

### 数据传输模型

- 一条一条处理：低延迟

    处理完一条数据后将其序列化到缓存，通过网络传输到下一个节点

- 一批一批处理：高吞吐

    处理完一条数据后，序列化到缓存中，当缓存写满时，持久化到本地硬盘，所有数据处理完成后，在传输到下一个节点

- 自己控制缓冲区大小

## flink vs spark

- 模型：flink是一条一条处理数据，spark是一次处理一批数据

- API: 提供封装后的高阶函数，可以直接拿来用

- 保证次数：通过事务可以保证对数据实现仅一次的处理

- 容错机制：通过checkpoint机制实现容错机制

- 状态管理：Spark streaming实现了基于DStream的状态管理，而flink实现了基于操作的状态管理

- 延时：flink的延时低于spark，flink是流处理，spark本质是批处理

- 吞吐：吞吐量都较高

## flink程序开发步骤

- 获取一个执行环境

- 加载、创建初始化数据

- 指定transaction算子

- 指定计算好数据的存放位置

- 调用execute()触发执行程序，此时程序才开始真正触发

## flink集群模式

- standalone模式：独立部署不依赖平台

- Flink on Yarn：依靠yarn来调度flink程序，需要hadoop集群

## flink standalone的高可用

## DataSource

- 基于文件：readTextFile(path)

- 基于socket：socketTextStream

- 基于集合：fromCollection

- 自定义输入：通过connector（连接器）安排apache kafka

## transaction

常见操作：

- map，flatmap

- filter

- keyBy

- reduce

- aggregations

- union：合并多个流，新的流会包含所有流中的数据，但是流类型相同

- connect：连接两个流，流类型可以不同，会对不同的流应用不同的方法

- split

- select

分区规则：

- random partition：随机分区

- rebalance：再平衡， 是一种数据分布操作，用于确保数据在并行任务实例之间均匀分布。它是一种改变数据流分区的操作，目的是使每个任务实例处理大致相等数量的数据，以提高任务的性能和吞吐量

- rescaling：重新调节， 是一种任务并行度调整操作，用于动态改变作业中任务实例的数量

- Custom partitioning：自定义分区

## sink任务

- writeAsText()

- print()

- 自定义输出：可以把数据输出到第三方介质中，系统提供了一批内置的connector

## 广播变量

广播变量允许编程人员在每台机器上保持一个只读的缓存变量，一个节点上可以有多个task，这样每一个task可以共享只读缓存变量。

## state和checkpoint

- State一般指一个具体的Task/Operator的状态，State数据默认保存在Java的堆内存中。

- 而CheckPoint（可以理解为CheckPoint是把State数据持久化存储了） 则表示了一个Flink Job在一个特定时刻的一份全局状态快照， 即包含了所有Task/Operator的状态。

## window

- timewindow

- countwindow

- 自定义window：一种是基于key，一种不基于key

## window分类

- 滚动窗口：tumbling window，窗口内的数据没有重叠

- 滑动窗口: sliding window，窗口内的数据有重叠

## window聚合分类

- 增量聚合：窗口每进入一条数据就计算一次

    - 实时计算，规模较大

- 全量聚合：全量聚合是指窗口触发的时候才会对窗口内的数据进行计算

    - 规模较小

## flink时间

- event time：事件产生的时间，它通常由事件中的时间戳描述

- ingestion time：事件进入flink的时间

- processing time：事件被处理时当前系统的时间

## watermark 水位线

- 基于Event time处理stream数据时会遇到数据乱序和延迟的问题

- 在基于window进行计算的时候，基于event time进行计算，如果数据乱序，会出问题

- watermark 可以将Watermark 理解为一个逻辑时钟，用于告诉系统在某个时间点之前的数据都已经被处理完毕。只有水位线越过窗口对应的结束时间，窗口才会关闭和进行计算。

## slot并行度

solt的数量通常与每个taskManager节点可用的CPU内核数成比例

并行度的设置可以从4个层面指定：

- 算子层面

- 执行环境层面

- 客户端层面

- 系统层面

oprator level > execution environment level > client level > system level
