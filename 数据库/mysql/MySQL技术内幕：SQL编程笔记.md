# 数据库分区笔记-mysql技术内幕第十章

## 10.1分区概述

1.   不是所有的数据引擎都支持分区，使用分区前应该了解数据引擎对分区的支持情况
2.   分区的过程是将一个表或者索引分解为多个更小，更可管理的部分。在逻辑上只有一个表或者一个分区，但是在物理上可能存在多个索引和多个分区。
3.   分区的类型：
     -   水平分区：将一个表中不同行的记录分配到不同的物理文件中
     -   垂直分区：将同一个表中不同的列分配到不同的物理文件中
     -   全局分区：数据存放在各分区中，但是所有数据的索引放在一个对象中
     -   局部分区：一个分区中既存放数据又存放索引（**先分区再索引**）

## 10.2 分区类型

1.   range分区：RANGE分区根据某个列的值的范围将数据划分到不同的分区中。每个分区包含一个特定范围内的值。适用于数据按顺序分布，并且常需要按范围进行查询的情况，例如按日期分区

     ```sql
     create table t(id int) engine=innodb
     partition by range(id)(
     	partition p0 values less than (10),
         partition p1 values less than(20)
     );
     
     insert into t select 9; -- 当id < 10时，插入分区p0
     insert into t select 10;  
     insert into t select 11; -- 当10 <= id < 20时，插入分区p1 
     ```

2.   list分区：LIST分区类似于RANGE分区，但它是根据枚举值列表将数据划分到不同的分区中。适用于数据离散分布的情况。

     ```sql
     create table t1(a int, b int) engine = innodb partition by list(b) (
     	partition p0 values in(1, 3, 5, 7, 9),
         partition p1 values in (0, 2, 4, 6, 8)
     );
     
     insert into t1 select 1, 1;
     insert into t1 select 1, 2;
     insert into t1 select 1, 3;
     ```

3.   hash分区：将数据均匀的分布到预先定义的各分区中，保证各分区的数据量大致是一样的。

     ```sql
     CREATE TABLE t_hash (
         a INT,
         b DATETIME
     )  ENGINE=INNODB PARTITION BY HASH (YEAR(b)) PARTITIONS 4;
     
     insert into t_hash select 1, '2010-04-01';
     
     ```

​	该sql会创建4个分区，数据分配的规则是使用mod(year(b), 4)

4.   key分区：key分区和hash分区类似，hash分区使用用户自定义函数作为hash函数，key分区使用数据库提供的函数进行分区，例如md5函数。

     ```sql
     CREATE TABLE t_key (
         a INT,
         b DATETIME
     )  ENGINE=INNODB PARTITION BY key (b) PARTITIONS 4;
     
     insert into t_key select 1, '2010-04-01';
     ```

5.   columns分区：range分区，list分区，hash分区和key分区，四种分区条件必须是整型（或者转化为整型）。columns分区可以使用非整型的数据进行分区。range columns分区可以对多个列的值进行分区。

     ```sql
     CREATE TABLE t_columns_range (
         a INT,
         b DATETIME
     )  ENGINE=INNODB PARTITION BY RANGE COLUMNS (B) (PARTITION p0 VALUES LESS THAN ('2009-01-01') , PARTITION p1 VALUES LESS THAN ('2010-01-01'));
     
     CREATE TABLE t_columns_string (
         first_name VARCHAR(255),
         last_name VARCHAR(255)
     )  ENGINE=INNODB PARTITION BY LIST COLUMNS (FIRST_NAME) (PARTITION p0 VALUES IN ('Jone' , 'Jim') , PARTITION p1 VALUES IN ('Kate' , 'ken'));
     
     CREATE TABLE t_columns_mul_range (
         a INT,
         b DATETIME
     )  ENGINE=INNODB PARTITION BY RANGE COLUMNS (A , B) (PARTITION p0 VALUES LESS THAN (5 , '2009-01-01') , PARTITION p1 VALUES LESS THAN (10 , '2010-01-01'));
     
     ```

## 10.3 子分区

子分区是在分区的基础上再进行分区，有时这种分区也被称为复合分区。

```sql
CREATE TABLE ts (
    a INT,
    b DATE
)  ENGINE=INNODB PARTITION BY RANGE (YEAR(b)) SUBPARTITION BY HASH (TO_DAYS(b)) SUBPARTITIONS 2 (PARTITION p0 VALUES LESS THAN (1990) , PARTITION p1 VALUES LESS THAN (2000) , PARTITION p2 VALUES LESS THAN MAXVALUE)
```

## 10.5 分区和性能

1.   大部分情况下，使用分区可以提升查询性能。

2.   有些情况下，分区可能提升效果不明显。例如分区前，索引数的深度和分区后索引树的深度一致，则本次分区不会对性能有较大的提升。

3.   有些情况下，分区可能会影响查询性能。当查询时没有使用分区键，此时相比未使用分区的原始表，性能可能更差。

     -   分区元数据：数据库需要逐个处理每一个分区的元数据，增加查询开销

     -   在存在分区的情况下，如果使用全表扫描，需要分别进入每一个分区进行扫描。会增加IO操作的数量和时间。

     -   索引树管理：查询时不包含分区键，则数据库需要在每一个分区的索引树中进行查找，导致更多的索引树被遍历操作。举例说明：原始表的索引高度是3，分区后高度是2，分区数量是4。则分区前，需要进行3次IO操作读取索引；分区后，需要执行2*4=8次IO操作读取索引。

         



