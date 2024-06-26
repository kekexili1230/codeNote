# 基于Apache Flink的流处理

## 第二章

### 数据交换策略

常用的数据交换策略有：

-   转发策略：发送端和接收端一对一数据传输
-   广播策略：将数据发往下游所有的算子。数据复制多份涉及网络通信
-   基于键值的策略：基于键值属性对数据进行分区
-   随机策略：将数据均匀分配给算子的所有任务

### 延迟和吞吐

延迟：输入接收事件，输出观察到事件处理结果的时间间隔。相关指标：平均延迟、最大延迟延迟的百分位数值。

-   对比批处理：批处理的延迟受制于最迟事件的时间，且天然收到批次大小的影响。

吞吐：单位时间内处理多少事件。相关指标：峰值吞吐。

### 数据流上的操作

数据接入：数据源可以是TCP套接字、文件、kafka主题或者传感器数据接口

数据输出：文件、数据库、消息队列或监控接口

转换操作：分别处理每个时间，产生新的数据流

滚动聚合：根据每一个到来的时间更新结果。例如求和、最小值、最大值

窗口操作：转换操作和滚动操作每次处理一个时间来产生输出，窗口操作可以收集并缓冲记录才能计算结果。窗口操作会创建有限事件集合，并基于有限集进行计算。

窗口策略的逻辑：

-   什么时候创建桶？
-   事件如何分配到桶？
-   桶内数据什么时候参与计算？
    -   计算函数如何计算

有哪些窗口类型的语义？

-   滚动窗口
-   
    -   什么时候创建桶？
        -   基于数量
        -   基于时间
    -   事件如何分配到桶？
        -   事件分配到长度固定且互不重叠的桶中
    -   什时候计算？
        -   窗口边界通过后，所有的时间发送给计算函数进行处理
-   滑动窗口
    -   什么时候创建桶？
        -   指定长度和滑动间隔来定义滑动窗口。滑动间隔决定每隔多久生成一个新的桶。
-   会话窗口
    -   会话窗口不会相互重叠，没有固定的开始或者结束时间。
    -   会话窗口在一段时间（session gap)内没有收到数据之后会关闭。新收到的数据发送到新的会话窗口中

### 时间语义

处理时间：当前流处理算子所在机器上的本地时间

事件时间：数据流中时间的时间。事件时间将处理速度和结果内容解耦，无论处理速度如何，生成同样的结果。

如何确定已经收到了所有指定时间点之前的事件？

利用水位线机制。水位线是一个全局进度指标，表示确信不会再有延迟事件到来的时间点。当算子接收到时间T的水位线，就认为不会再收到任何时间戳小于或者等于T的事件了。算子收到水位线，可以触发窗口计算。

## 第三章 Flink架构

### flink所需的组件

-   JobManager
    -   主进程，控制应用程序的执行
    -   接收逻辑Dataflow图，执行代码、依赖库jar文件
    -   将Dataflow转化为物理Dataflow图
    -   从ResourceManager申请资源slot，将物理Dataflow图分发给slot执行
    -   负责所有集中协调的操作。例如检查点、保存点以及状态恢复
-   ResourceManager
    -   针对不同的环境和资源提供者，flink提供不同的ResouceManager。
    -   负责管理flink的处理资源slot
-   TaskManager
    -   flink的工作进程
    -   每一个TaskManager提供一定数量的处理槽，TM启动后会向ResourceManager注册处理槽。
    -   如果资源充足，RM通常会为不同的应用分配不同的TM；但是也可能共用相同的TM，但是使用不同的slot
-   Dispatcher
    -   提供了一个REST接口让我们提交需要执行的应用。提交后启动一个JM并将应用转交给它。

### 任务执行

TM可以执行多个任务，这些任务可以

-   数据并行（属于同一个算子）
-   任务并行（属于不同的算子）
-   作业并行（来自不同的应用）

## 第六章 基于时间和窗口的算子

### 自定义窗口算子

#### 分配器

-   当元素进入窗口算子时会被移交给窗口分配器。分配器决定了元素应该放入哪个窗口。如果目标窗口不存在，则会创建它。
-   如果窗口算子时增量聚合函数，则新加入的元素会立即执行聚合，结果作为窗口内容存储；如果窗口算子是全量窗口函数，则会将元素保存在ListState上。

#### 触发器

元素加入窗口后会传递给触发器。触发器定义如下动作：

-   如何触发执行计算
-   核实清除自身以及保存的内容
-   触发成功后执行窗口函数（聚合函数、全量窗口函数）并发出结果

#### 移除器

允许在ProcessWindowFunction调用之前或者之后引入，可以用来从窗口中删除以及收集的元素。

#### 窗口的生命周期

windowAssigner将元素分配给窗口；触发器决定何时对窗口执行计算；窗口函数确定实际的计算逻辑。

-   窗口何时创建？
    -   windowAssigner首次向它分配元素时创建，因此每一个窗口至少一个元素。
-   窗口的内容有哪些？
    -   分配给窗口的元素
    -   如果窗口函数时增量聚合函数时，中间状态变量。
-   触发器计时器
    -   在触发器中注册计时器，用于在将来某个时间点触发回调

#### 窗口分配器

#### 触发器

每次调用触发器都会生成一个TriggerResult，决定窗口接下来的行为。值可能是

-   continue：什么都不做
-   fire：如果窗口算子配置了ProcessWindowFunction。就会调用该函数
-   PURGE：完全清楚窗口内容，并删除窗口自身以及元数据
-   FIRE_AND_PURGE：先进行窗口计算，随后删除所有状态以及元数据

### 处理迟到的数据

-   简单的将其丢弃
-   将迟到事件重定向到单独的数据流中
-   根据迟到事件更新并发出计算结果
-   
