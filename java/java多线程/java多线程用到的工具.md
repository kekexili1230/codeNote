# 多线程用到的工具

## java visualVM


## java jconsole


## jmap内存使用情况

## jstack

## 如何生成dump文件？

1. jmap: Java Memory Map的缩写，是JVM提供的一个命令行工具，用于生成堆转储文件，查看堆内存的详细信息。

- jps：查看虚拟机进程

- jmap：根据进程信息生成dump文件

    - jmap pid: ```jmap -dump:format=b,file=20200425.dump 24362```

    - jmap -heap pid : 显示Java堆详细信息，打印摘要信息

- jstack：线程快照，当遇到死锁时，可以分析该日志



2. 在启动命令中增加参数：

```-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=目录+产生的时间.hprof```

