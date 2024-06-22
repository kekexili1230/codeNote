# Spark 笔记

## Spark SQL笔记

1.   RDD，DataSet，DataFrame三种数据类型的对比

     -   RDD中存储非类型化的数据，其中的元素可以是任意类型的

     -   DataSet中存储类型化的数据，其中的元素类型完全相同

     -   DataFrame是一种无类型的数据抽象，类似关系数据库中的表。

         -   Row对象：DataFrame中的元素是一个Row对象，每一个Row对象可以包含不同的结构
         -   列信息：Row对象可以包含不同的结构即列名和列类型。DataFrame中存储所有的列名和列类型，是所有Row对象列信息的合集。

         