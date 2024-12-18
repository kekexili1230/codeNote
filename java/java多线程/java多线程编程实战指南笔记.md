# java多线程编程实战指南笔记

## 第一章 走进Java世界中的线程

### 1.3 Java线程API简介

- 编号：不能做唯一标识

    - 线程运行结束，编号可能被后续线程使用。

    - 重启后，线程编号会发生变化
- 名称：不同的线程可以设置相同的名称
- 线程类别：Deamon，必须在线程启动前设置

    - 用户线程

    - 守护线程：程序中只有守护线程，则虚拟机会结束。
- 优先级：java定义了1-10个优先级，默认为5

### 1.4 无处不在的线程

-   main线程：负责执行main方法

-   垃圾回收线程

### 1.5 线程的层级关系

线程A执行的代码创建了线程B，则线程A是线程B的父线程。

-   父线程是守护线程（用户线程），则子线程默认也是守护线程（用户线程）。除非显式调用setDaemon
-   子线程的优先级默认等于父线程的优先级
-   子线程和父线程的声明周期没有必然联系

### 1.6 线程的生命周期状态

java的线程状态可以通过Thread.getState函数获取。

- new

- runnable

    - ready：可以被线程调度器调度使之处于running状态

    - running：正在运行。如果线程执行yield方法，状态可能由running变为ready

- blocked

- waiting

- time-waiting

- terminate


### 1.7 线程的监视

查看线程的转储（thread dump），线程转储是一个线程快照信息，包含的信息：

- 线程的属性

- 生命周期状态

- 线程的调用栈

- 锁

获取线程转储文件的方法

- jstack -l pid 

### 1.9 多线程的优势和风险

多线程的优势：

- 提高系统的吞吐率：多个线程并发执行，当一个线程IO等待时，其他线程仍然可以执行

- 充分利用多核：

- 最小化最系统资源的使用：相比多进程，多线程可以共享进程申请的资源

- 简化程序的结构：

- 提高响应：在多线程编程中，一个慢的响应操作不会影响其他操作


多线程的劣势：

-   线程安全：多个线程共享数据会产生数据一致性问题

-   线程活性：死锁问题 

-   上下文切换：

-   可靠性：相比多进程编程，单进程多线程程序，如果进程崩溃，则所有的线程均中断



## 第二章 多线程编程的目标和挑战

### 2.1 串行、并发和并行

串行：分别按照顺序执行A、B、C

并发：同时执行A、B、C三件事，时间片轮转方式执行

并行：三个处理器真正同时执行A、B、C三件事。并行是一种更为严格、理想的并发。

### 2.2 竟态

并发编程存在竟态问题。什么是竟态？

竞态条件（Race Condition）是指在多线程编程中，多个线程访问共享资源，并且最终的结果依赖于线程执行的相对顺序，但这个顺序是不确定的。竞态条件可能导致程序的行为不符合预期，因为线程的执行顺序可能会影响最终结果。

竟态的两种模式：

- read-modify-write

- check-then-act

### 2.3 线程安全性

对象的线程安全性：一个类在单线程环境下能够运行正常，并且在多线程环境下，使用方无须做额外改变也能正常运行，则我们称其是线程安全的。

线程安全问题变现在三个方面：原子性、可见性、有序性

### 2.4 原子性

1. 什么是原子性？

涉及**对共享变量**访问的操作，若操作从其执行线程**以外的任意线程来看是不可分割的**，则该操作就是原子操作。

2. java实现原子性的方式

- 共享变量

    - 锁

    - cas指令

- 基本数据类型

    - 非long/double：读写具有原子性

    - long/double：volatile 修饰可以确保原子性

    - 对任何变量的读操作都具有原子性

**原子操作可以消除竞态**

### 2.5 可见性

1. 什么是可见性问题？

一个线程对共享变量更新后，后续访问该变量的线程可能无法立刻看到这个更新结果，这就是线程安全的可见性问题。

多线程程序在可见性方面存在问题意味着某些线程读取到了旧数据。


2. 为什么存在可见性问题？

和计算机存储系统有关。处理器不是直接读取内存，而是通过寄存器、高速缓存、写缓冲器、无效化队列等部件执行内存读写操作。每一个处理器都有自己的处理器缓存。

3. CPU写数据的顺序是：寄存器 -> 写缓冲器 -> 高速缓存 -> 主内存

4. 什么是缓存一致性协议？

通过缓存一致性协议可以读取其他处理器高速缓存中的数据，并将读到的数据更新到该处理器的高速缓存中，实现**缓存同步**

缓存同步可以使得一个处理器读取到另一个处理器对共享变量所做的更新：

- 冲刷处理器缓存：将处理器对共享变量所做的更新从**写缓冲器**写入**高速缓存**中，这个过程称为flush处理器缓存。冲刷处理器缓存可以确保其他线程读取到共享变量的更新。

- 刷新处理器缓存：处理器在读取共享变量时，如果其他处理器已更新共享变量，本处理器必须从其他处理器的高速缓存中进行缓存同步，这个过程叫做refresh处理器缓存。刷新处理器缓存可以确保读取到最新的共享变量。

**通过冲刷处理器缓存和刷新处理器缓存可以确保可见性**

5. java如何保证可见性？

- volatile

- lock

6. 原子性和可见性的联系区别？

- 原子性可以保证一个线程读取到的共享变量的值要么是变量的初始值要么是相对新值，而不会是一个中间结果。

- 可见性描述的是一个线程对共享变量的更新对另外一个线程而言是否可见。

- 原子性确保线程对共享变量的修改不受干扰；可见性确保其他线程能够及时看到该修改。

### 2.6 有序性

有序性指在什么情况下一个处理器上运行的一个线程所执行的内存访问操作在其他线程看来是乱序的。

#### 2.6.1重排序

在多核处理器环境下，按照代码顺序执行可能是没有保障的：

- 编译器可能改变操作的先后顺序

- 处理器可能不是完全按照程序的目标代码所指定的顺序执行指令

- 一个处理器上执行的操作，在其他处理器看来执行顺序和代码顺序不一致

重排序是对内存访问的优化，在不影响单线程程序正确性的情况下提升程序的性能。

2. 内存操作顺序相关术语：

    - 源代码顺序


    - 程序顺序：字节码顺序和机器码顺序


    - 执行顺序：处理器实际执行顺序


    - 感知顺序：其他处理器看到的内存访问顺序


3. 重排序可以分为：指令重排序和内存重排序

#### 2.6.2指令重排序

java平台包含两种编译器：静态编译器和动态编译器。前者的作用是将java源代码编译为字节码(.class)文件，后者的作用是在运行时将字节码编译为java虚拟机的本地代码（机器码）。动态编译器可能执行指令重排序。

处理器也可能对指令进行重排序，乱序执行。

#### 2.6.3 内存重排序

1. 四种类型的内存重排序：

    - loadload重排序：处理器先后执行两个读内存操作L1和L2，其他处理器的感知顺序可能是L2、L1


    - storestore重排序：处理器先后执行两个写内存操作W1和W2，其他处理器对这两个内存操作的感知顺序是W2、W1


    - loadstore重排序：处理器先执行读操作L1，再执行写操作w2，其他处理器对这两个内存操作的感知顺序是W2->L1


    - storestore重排序：处理器先执行写操作W1，再执行读操作L2，其他处理器对这两个内存操作的感知顺序可能是L2->W1


#### 2.6.4 貌似串行语义

1.   数据依赖关系：两个指令访问同一个变量，且其中一个操作为写操作，则这两个操作之间存在数据依赖关系：

     - 写后读


     - 读后写


     - 写后写


2.   貌似串行语义：存在数据依赖关系的执行不会被重排，这样从单线程的角度保证重排后程序的运行结果不影响程序的正确性，从而给单线程程序造成一种假象，指令是按照源代码顺序执行的。
2.   由于存在指令重排序和内存重排序，但是为了确保程序在单线程中的正确执行，重排序必须符合**貌似串行语义**法则。因此尽管实际发生了重排序，但是程序的执行效果是顺序执行的。

#### 2.6.5 保证内存访问的顺序性

1.   如何在**多线程环境**中保证内存访问的顺序性？

     - 从底层角度来说，通过调用处理器提供的内存屏障来实现


     - 从java语言的角度，volatile关键字，sync关键字，lock


2.   可见性和有序性的关系？

     - 可见性是有序性的基础。先可见，然后有序


     - 有序性影响可见性。重排序后，一个线程对共享变量的更新对于另外一个线程而言可能变得不可见。



### 2.7 上下文切换

1. 什么是上下文切换？

    一个线程被暂停（被剥夺处理器的使用权），另外一个线程被选中开始或者继续运行的过程就叫做线程**上下文切换**。在进行上下文切换时需要保存和恢复相应线程的进度信息，这个进度信息就被称为**上下文**，一般包括通用寄存器的内容和程序计数器的内容。上下文切换有两个过程：
    
    -   切出：一个线程被剥夺处理器的使用权而被暂停运行，同时将上下文信息保存到内存中
    
    
    -   切入：一个线程被选中用处理器开始或者继续运行，同时从内存中加载被选中的上下文
    

2. 什么时候发生上下文切换？

    从java应用的角度，一个线程的生命周期在running状态和非running状态（blocked，waiting，timed_waiting）之间切换的过程就是一个上下文切换的过程：

    -   由running -> 非running，线程暂停，线程**切出**，操作系统保存上下文
    -   由非running -> running，线程唤醒，唤醒并不代表可以立刻占用处理器。只有被唤醒且占用处理器的时候，操作系统会恢复上下文，线程**切入**

3. 什么会触发上下文切换？

    - 自发性上下文切换

        - Thread.sleep

        - Object.wait

        - Thread.yeild

        - thread.join

        - LockSupport.park


    - 非自发性上下文切换

        - 时间片用完

        - 优先级更高的线程需要被运行


4. 上下文切换的开销

    - 直接开销

        - 操作系统保存和恢复上下文所需的开销

        - 线程调度器的开销（决定哪一个线程占用处理器）

    - 间接开销

        - 处理器高速缓存重新加载的开销

        - 可能导致一级高速缓存中的内容被冲刷

5. 如何测量上下文切换的开销？

    在linux中使用perf命令来监视java程序运行过程中的上下文切换的次数和频次。


## 第三章 java线程同步机制

### 3.1 线程同步机制简介

1.   什么是线程同步机制？

     用于协调线程间**数据访问**和**活动**的机制，这些机制用于保障线程安全。

2. 哪些机制用于协调线程间数据访问？

    锁：内部锁Sync关键字，显式锁Lock接口，轻量级的同步机制volatile关键字，CAS和原子量

3. 哪些机制用于协调线程间活动？

    等待和通知wait/notify，条件变量，倒计时协调器countDownLatch，栅栏，阻塞队列，信号量

### 3.2 锁概述

根据java虚拟机对锁的视线方式划分，java平台的锁包括内部锁和显式锁：

- 内部锁通过sync关键字实现

- 显式锁通过lock接口实现类实现

#### 3.2.1 锁的作用

锁的作用：保证原子性、可见性和有序性

- 保证原子性：通过互斥保证原子性，通过互斥将多线程对共享数据的并发访问改为串行访问。

- 保证可见性：

    - 在java中锁的获取隐含着refresh处理器缓存。读线程获取锁后，可以将写线程修改的更新同步到读线程处理器高速缓存中。

    - 锁的释放隐含着flush处理器缓存。写线程释放锁的时候，对共享变量的修改能够被推送到该线程处理器的高速缓存中。

- 保证有序性：写线程在临界区中所执行的一些列操作在读线程所执行的临界区看起来就像是顺序执行的。

#### 3.2.2 锁相关的概念

- 锁的申请：可重入性

- 锁的调度：公平锁和非公平锁

- 锁的粒度：保护数据量的大小

#### 3.2.3 锁的开销

- 申请、释放锁产生的开销

- 导致上下文切换的开销

### 3.3 内部锁：sync

1.   java中任意对象都有唯一一个与之关联的锁，被称为监视器锁和内部锁。

2.   sync关键字修饰代码块时，需要使用监视器对象，监视器一般用final关键字修饰，为什么？

     作为锁句柄的变量通常采用final修饰，因为变量改变会导致执行同一个同步块的多个线程实际上使用不同的锁。使用final关键字修饰监视器，可以避免监视器变量引用新的对象，从而使得修改前后同步机制失效

3.   使用sync修饰静态方法时，相当于使用类对象（class 对象）作为引导锁

4.   内部锁是如何调度的？

- 虚拟机为每一个内部锁分配一个入口集，入口集记录等待锁的线程

- 多个线程申请内部锁

    - 申请成功，获取锁，进入临界区

    - 申请失败，进入入口集，状态变为BLOCKED，变为等待线程

- 线程释放内部锁，虚拟机从入口集中选择唤醒一个线程，同时和其他未进入入口集并同时申请锁的线程竞争锁

    - 申请成功，获取锁，进入临界区

    - 申请失败，进入入口集，状态变为BLOCKED，变为等待线程

注：唤醒入口集中的线程，并不意味该线程就能获取锁

5.   sync关键字是如何使用的？

-   修饰方法

    -   修饰静态方法

    -   修饰实例方法

-   修饰代码块

6.   内部锁不会导致锁泄露，为什么？

     java编译器在将同步代码编译为字节码的时候，对临界区中可能抛出而又未捕获的异常做了特殊的处理，不会造成锁泄露。

### 3.4 显式锁：lock

1. 使用函数：lock，unlock，trylock

2. 显式锁和内部锁的比较

- 内部锁是基于代码块的锁

- 显式锁是基于对象的锁；显式锁支持中断，公平锁和超时设置，显式的申请和释放

### 3.5 锁的适用场景（无论是显式锁还是内部锁）

- check-then-act

- read-modify-write操作

- 多个线程对多个共享数据进行更新，为了保证共享数据能够一致，需要使用锁

### 3.6 锁的底层实现：内存屏障

1. java虚拟机如何实现flush和fresh处理器缓存？

通过内存屏障实现。

2. 什么是内存屏障？

内存屏障是一种硬件或软件屏障，用于确保特定顺序的内存访问操作不会被重排序或优化。内存屏障被插入两个指令之间，**禁止编译器和处理器重排序**从而保证有序性，同时这些指令也有副作用**刷新处理器缓存和冲刷处理器缓存**，从而保证可见性。

- 根据可见性保障划分

    - load barrier(加载屏障)：

        - refresh 处理器缓存
        
        - java虚拟机在MonitorEnter（申请锁）之后插入加载屏障

    - store barrier(存储屏障)：

        - flush处理器缓存
        
        - java虚拟机在MonitorExit(释放锁)对应的机器码指令后插入存储屏障。

- 按照有序性保证划分

    - 获取屏障：acquire barrier，

        - monitorEnter包含读操作
        
        - 在读操作（ monitorEnter）之后加入acquire barrier，禁止读操作（ monitorEnter）和屏障之后的读写操作之间重排序。

        - 获取屏障禁止临界区中的读写操作重排到临界区之前

    - 释放屏障：release barrier

        - monitorRelease包含写操作

        - 在写操作（monitorRelease）之前加入release barrier，禁止写操作（monitorRelease）和屏障之前的读写操作之间重排序。

        - 释放屏障禁止临界区中的读写操作重排到临界区之后

结合锁的排他性，一级获取屏障、释放屏障，使得临界区具有原子性。


3. 在一个典型锁结构中，内存屏障和锁的布局如下顺序所示：

- monitorEnter

    - lock，排他性

    - 包含读操作
    
- Load barrier

    - refresh处理器缓存，确保可以读取到最新的值，保证可见性

- acquire barrier

    - 在读操作（moniterEnter）后，禁止临界区代码和moniterEnter重排序，禁止临界区代码排到锁之前

- 临界区：读、写共享变量

- relase barrier
    - 在写操作（MonitorEnter）后，禁止临界区代码和monitorExit重排，禁止临界区代码排到锁之后

- monitorExit

    - unlock，释放锁

    - 包含写操作

- store barrier

    - flush处理器缓存，确保共享变量的修改值刷新到高速缓存中


### 3.8 轻量级同步机制：volatile

1. volatile变量不会被编译器分配到寄存器进行存储，对volatile变量的读写操作都是内存访问。

2. volatile可以保证可见性和有序性，但是不能保证写volatile变量的原子性，volatile不会引起上下文切换。

3. volatile的作用：

    - 对volatile修饰的double/long类型变量的写操作具有原子性

        - 仅保证对修饰变量的写操作具有原子性，而不表示对volatile变量的赋值操作具有原子性。


    - 保证有序性


    - 保证可见性


4. 对volatile写操作的内存屏障布局

    - release barrier

        - 在对volatile变量写操作后，禁止volatile写操作和该操作之前的任何读写操作进行重排序。保证有序性


    - volatile变量写操作


    - store barrier
    
        - flush处理器缓存，确保共享变量的修改值刷新到高速缓存中，确保对其他处理器可见


5. 对volatile读操作的内存屏障布局

    - load bariier

        - refresh处理器缓存，确保可以读取到最新的值，保证可见性


    - 读volatile变量


    - acquire barrier
    
        - 在读操作后，禁止读操作和和之后的读写操作重排，保证有序性

6. 对不同类型的变量使用volatile关键字作用不同：

    - 对基础类型变量：保证读线程能够读到最新的值
    - 对引用型变量：保证读线程能够读取到一个指向对象的新的应用，而不保证该对象实例的值是否为最新

7. volatile的典型应用场景

    - 使用volatile变量作为标志位


    - 使用volatile保证可见性


    - 使用volatile变量替代锁。多个线程共享一组变量，把这一组变量封装成一个对象，对变量的更新操作变成创建新的对象实例，然后将新的对象实例赋给volatile变量


    - 实现简易版本的读写锁：写操作加锁，读操作不加锁，读写操作使用volatile变量（todo 重点理解这句话）

8. 多线程环境下的static字段

    - 类采用延迟加载的技术，当类被虚拟机加载后，类的静态变量为默认值（null, 0, false）

    - 当程序首次使用静态变量和静态方法时，类被初始化（执行静态变量的赋值，静态初始化块）

    - 在多线程环境中，仅仅能够保证线程读到相应字段的初始值，而不是最新值。要保证可见性，需要使用同步机制

9. 多线程环境下的final字段

    当一个对象被发布到其他线程时，该对象的所有final字段都是初始化完毕的，即其他线程读取final字段都是初始化后的值，但是非final字段则不保证。

#### 3.8.2 volatile变量的开销

volatile变量的读、写操作都不会导致上下文切换。

### 3.10 非阻塞同步：CAS与原子变量

#### 3.10.1 cas操作

cas只是保障了共享变量更新这个操作的原子性，但是并不保障可见性。因此仍需使用volatile修饰，完整的代码实例如下所示：

```java

public class CASTest {
    private volatile long count;

    public void increment() {
        long oldvalue;
        long newValue;

        do {
            oldValue = count;
            newValue = oldValue + 1;
        } while (!compareAndSwap(oldValue, newValue));

    }
}

```
#### 3.10.2 原子变量类

## 第五章 线程间协作

### 5.5 生产者-消费者模式

1.   生产者和消费者模式的优点：可以使程序中原本串行的处理得以并发化
2.   生产者和消费者模式中如何在不同线程中传递数据？通过阻塞队列
3.   阻塞队列不仅能够传递数据，还在一定程度上平衡生产者和消费者处理能力。

#### 5.5.1 阻塞队列

在java中的阻塞队列BlockingQueue：

- ArrayBlockingQueue：使用数组作为存储空间，put和take操作使用同一个锁（可能操作同一块内存）

- LinkedBlockingQueue：动态分配内存，put和take操作使用不同的锁

- SynchronousQueue：生产者put时阻塞，直到消费者take之后再返回；消费者take时阻塞，直到生产者put后返回

三种阻塞队列的使用场景选择：

- LinkedBlockingQueue：适合在生产者和消费者之间并发程度较大

- ArrayBlockingQueue：适合生产者和消费者之间并发程度较低

- SynchronousQueue：适合生产者和消费者处理能力相差不大

## 第六章：保证线程安全的设计技术

从面向对象设计的角度出发介绍几种保证线程安全的常用技术

### 6.1 java运行时存储空间

- 堆空间：存储对象实例

- 栈空间：每个线程都有栈空间，线程创建时分配。

    - 调用方法前，会为每一个方法调用创建一个栈帧，存储局部变量和返回值。

    - 线程对局部变量以及只有通过当前线程的局部变量才能访问的对象具有线程安全性。

- 非堆空间：存储常量以及类的元数据。类的元数据包括类的静态变量，类的方法以及方法相关参数

堆空间和非堆空间是线程间可共享的的空间，表现为实例变量和静态变量是线程间可共享的；栈空间是线程的私有空间，表现为线程对局部变量以及只有通过当前线程的局部变量才能访问的对象具有线程安全性。

### 6.2 无状态对象

1. 对象是数据和操作的封装，数据包括：

- 实例变量

- 静态变量

- 该对象引用的其他对象的实例变量或者静态变量

对象所包含的数据称为对象的状态，实例变量和静态变量成为状态变量。

2. 如果一个类的同一实例被多个线程共享，但不会使这些线程存在共享状态，则称这个类以及任意实例被称为无状态对象：

- 无状态对象：不包含任意实例变量，不包含任何静态变量或者静态变量都是只读的

- 有状态对象：

    - 状态可变对象

    - 状态不可变对象

无状态对象具有固有的线程安全性，有两层含义：

- 客户端代码在调用该对象的任何方法时都无须加锁

- 无状态对象自身的方法实现也无须加锁

3. 不可变对象：是指一经创建其状态就保持不变的对象。不可变对象需要满足如下条件

- 类本身用final修饰，防止创建子类改变其行为

- 所有字段都用final修饰

- 对象在初始化过程中没有逸出

- 如果字段引用了其他状态可变的对象，则字段必须用private修饰，相关方法返回字段时，进行防御性复制

### 6.4 线程持有对象











