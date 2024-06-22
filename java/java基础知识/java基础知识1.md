# java基础知识

## comparator和comparable的比较

- Comparable 是“比较”的意思，而 Comparator 是“比较器”的意思；

- Comparable 是通过重写 compareTo 方法实现排序的，而 Comparator 是通过重写 compare 方法实现排序的；

- Comparable 必须由自定义类内部实现排序方法，而 Comparator 是外部定义并实现排序的。

## instanceof

在Java中，instanceof 是一个运算符，用于测试一个对象是否是特定类的一个实例或者是其子类的实例。

## equals方法的实现

- 调用父类的equals方法，如果为true，继续执行

- 判断是否和this相等

- 判断是否和null相等

- 调用getclass类方法，判断两者是否相等

- 强制类型转换，然后比较字段是否相等。

## 对象的构造

- 按照类定义的顺序执行静态初始化语句和初始化块

- 按照类定义的顺序执行域的初始化语句和初始化块

- 执行构造器

## 接口和抽象类的区别

- 抽象类是对类整体进行抽象，包括属性和行为；接口只是对行为进行的一种抽象

- 抽象类中可以含有静态代码和静态代码块；接口不能

- 一个类只能继承一个抽象类，但是可以实现多个接口

## java中的异常

- Error：Error类层次结构描述了Java运行时系统的内部错误和资源耗尽错误。非受查异常

- RuntimeException：程序错误导致的异常属于RuntimeException。非受查异常

- IOException: 

## 多线程编程

### 线程池

1. Runnable、Callable、Future之前的区别和联系

    - Runnable接口的run方法实现任务执行逻辑，run函数没有返回值，启动线程运行任务，本质通过Runnable接口实现。


    - Callable接口类似于 Runnable，但是它可以返回任务执行的结果，并且可以抛出异常。Thread类不能直接使用Callable接口。


    - Future接口表示推迟计算的结果。该接口提供方法检查任务是否完成，获取任务的结果。


2. FeatureTask

    FeatureTask类实现了RunnableFuture接口，该接口继承了Runnable和Future接口。该类用于将Runnable和Callable任务提交给线程执行，并通过Future接口获取任务的执行结果。

3. ExecutorService

    ExecutorService接口是Java中用于管理和执行异步任务的高级接口。它扩展了Executor接口，提供了更多的功能和控制，使得在多线程环境中更加灵活地管理任务的执行，包括：

    - 任务的提交和获取任务结果Feature


    - shutdown任务


    - 检查任务是否被关闭


4. Executors和ThreadPoolExecutors

    Executors提供了创建线程池的静态方法，这些静态方法调用ThreadPoolExecutors创建线程池。


## 函数式编程

1. 函数式编程的优点：行为参数化

2. lambda表达式相比匿名内部类更加简洁

3. 函数式接口：只有一个抽象方法的接口

4. lambda表达式允许你直接以内联的形式为函数式接口的抽象方法提供实例，并把lambda表达式作为函数式接口一个具体实现的实例。

5. 方法引用：方法引用可以被看作仅仅调用特定方法的Lambda的一种快捷写法。


## 流

1. 流允许以声明性方式处理数据集合，类似SQL语句，优点：

    - 代码易读


    - 可以把几个基础操作链接在一起构成流水线操作


    - 可以利用多线程



2. 源

    流会使用一个提供数据的源，例如集合、输入和输出资源。

3. 数据处理操作

    - 中间操作，结果还是一个流

        - filter、map、reduce、find、match、sort、collect、limit


    - 终端操作，关闭流

        - collect


4. 流与集合之间的区别

    流与集合之间的差异在于什么时候计算。集合包含数据结构中的所有值，流则按需计算，无需全部元素


5. 流操作

    - 筛选操作：filter


    - 切片操作：skip，limit


    - 映射：map，flatmap


    - 查找：findFirst、findAny返回元素


    - 匹配：anyMatch、allMatch是否存在，返回boolean值


    - 归约操作：reduce(parm1, (parm1, parm2) -> item)


6. 构建流

    - 由数值创建流: Stream.of(parm1, parm2, ...)


    - 由数组创建流：Arrays.stream(stream)


    - 由文件生成流：Files.lines


    - 由函数生成流：stream.iterate, stream.generate

      - stream.iterate(parm1, itemA -> itemB)
          - stream.generate(Supplier<T>)
      


7. 收集操作

    - 归约操作

      - Collectors.counting, Collectors.joining, Collectors.reducing 

    - 分组、分区

      - Collectors.groupingBy, Collectors.partitionBy
      

## 反射

能够分析类能力的程序称为反射。

### 获取class类型的方法

- 通过类：Person.class

- 通过实例: person.getClass()

- 通过名称 : Class.forName("class full path");

### 分析类的结构

- Field

- Method

- Constructor

### 内部类

1. 内部类可以访问外部定义类的所有域。

2. 如何实现上述功能呢？编译器修改了所有的内部类的构造器，添加一个外围类引用的参数

3. 通过如下方式创建内部类实例

```java

OuterClass outer = new OuterClass();
OuterClass.InterClass inter = outer.new InterClass();

```


### 泛型

- ? extents E 表示 some type that is a subtype of E.

- ? super T 表示 some type that is a supertype of E.

- get 和 put 原则：

    - use an extends wildcard when you only get values out of a structure

    - use a super wildcard when you only put values into a structure, 
    
    - and don’t usea wildcard when you both get and put. 


### 数组、泛型、通配符

#### 数组子类型是泛型

如果T是S的子类型，则T[]是S[]的子类型

```java

Integer[] ints = new Integer[] {1, 2, 3};
Number[] nums = ints; // 正常
nums[2] = 3.4;  //运行时错误

```

程序在运行到第三行的时候，会监测目标类型是Integer，但是希望存储double类型的数据，程序报错

#### 泛型的子类型不是协变的

如果T是S的子类型，但是List<T>不是List<S>的子类型

```java

List<Integer> ints = Arrays.asList(1, 2, 3);

List<Number> nums = ints;  // 编译时报错

```

#### 通配符可以使泛型的子类型变为协变

如果T是S的子类型，则List<T>是List<? extends S>的子类型

```java

List<Integer> ints = Arrays.asList(1, 2, 3);
List<? extends Number> nums = ints; // 编译通过
nums.add(3); //编译时报错，不能向? extends Number中赋值

```

如果S是T的父类型，则List<S>是List<? Super T>的子类型


### ReentrantLock和syncronize有什么区别？

- 两者都是可重入锁

- ReentrantLock可中断，可设置超时时间，可设置公平锁等，可以绑定多个条件变量

### condition和object中的方法异同

- condition.await()，condition.signal()，condition.signalAll()

- object.wait()，object.notify(), object.notofyAll()

### Semaphore信号量的使用

### atomicInteger的使用

- atomicInteger使用机器指令而不是锁实现自增的原子性，例如incrementAndGet函数，DecrementAndGet函数，自增和自减过程不会被中断。

- LongAdder LongAdder，设置多个累加单元，不同线程累加不同的累加单元，最后在将其汇总，减少了cas重试失败，从而提高了性能。

### 乐观锁

乐观锁的思想：默认不会写冲突，先执行写操作，如果错误则重试。乐观锁的执行步骤：

- 获取版本号

- 执行操作（乐观认为执行过程不会有冲突）

- 执行CAS操作，如果失败（执行冲突）则重试上述步骤。

乐观锁的实现：CAS(compare and swap)

乐观锁遇到的问题

- ABA问题

    - 什么是ABA问题？

    具体来说，假设线程A首先读取了共享变量的值为X，然后线程B修改了这个共享变量的值为Y，接着又修改回为X。此时，线程A再次尝试使用CAS来比较并交换，发现当前值仍然是X，符合预期，于是CAS操作成功。然而，实际上在操作过程中，共享变量的值经历了从X到Y再到X的变化，这就是ABA问题。

    - 如何解决ABA问题

    使用版本号AtomicStampedReference<T>。每次更新操作都要增加版本号，每次更新时同时检查值和版本号；

- 自旋时间可能过长

- 多个共享变量不能保证原子性

### 锁的优化

- 减少锁的持有时间

- 减少锁的粒度，讲一个大对象拆为一个小对象，大大增加并行度，减少锁的竞争

- 锁分离：使用读写锁

### 阻塞队列

当试图向队里中增加元素而队列已满，或者想从队列中移出元素而队列为空时，阻塞队列导致线程阻塞。
常用的阻塞队列包括：LinkedBlockingQueue，ArrayBlockingQueue，阻塞队列的方法有三种：

- 阻塞：put, take

- 异常：add, remove

- 超时：offer, poll

### CyclicBarrier(障栅)

障栅的作用是同步线程的执行进度，当线程到达障栅后会等待，直到所有的线程都到达后，才能继续向下执行。使用障栅的步骤：

- 创建变量：CyclicBarrier barrier = new CyclicBarrier(num, runaction); 

- 线程执行函数中，在barrier处等待：barrier.await();

- 当num个线程都到达barrier处时，执行回调函数runaction，然后所有线程继续向下执行。

### 倒计时门栓

CountDownLatch 的主要作用是在一个或多个线程执行的过程中，等待其他线程的操作完成，然后再继续执行。使用步骤如下：

- 主线程创建一个变量： CountDownLatch latch = new CountDownLatch(NUM_THREADS);

- 主线程将该变量赋给子线程，然后主线程在该变量上等待：latch.await()

- 子线程执行到某个节点后，调用latch.down()

- 如果NUM_THREADS个子线程都执行latch.down()，则主线程可以继续向下执行


### volatile 关键字的作用

- 可见性： 当一个线程修改了被 volatile 关键字修饰的变量的值，其他线程能够立即看到这个修改。

- 禁止指令重排

- 不是原子性


### 如何在两个线程之间共享数据

### threadlocal 线程本地变量

在一个线程的声明周期起作用，减少同一个线程内多个函数之间公共变量的传递


## 异常

java的异常分类：

- Error ： 系统内部错误和资源耗尽错误，程序无法处理

- Exception ：程序可以处理的异常

  - RuntimeException 非受查异常 运行时错误

  - IOException  受查异常 编译器报错



