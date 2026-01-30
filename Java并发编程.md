## 多线程

### Java的内存模型（JMM）介绍一下

JMM规定了所有变量都存在主内存(Main Memory)中。
但每个线程都有自己的工作内存(Working Memory)（类比CPU缓存）。

- 读数据：线程必须先把变量从主内存拷贝到自己的工作内存，才能使用。
- 写数据：线程只能改自己工作内存里的副本，改完后再刷回主内存。
- 隔离性：线程A看不到线程B工作内存里的数据，它们只能通过主内存来“传话”。

JMM有三大特点：

- 原子性(Atomicity)
  - 定义：一个操作要么全做，要么全不做，不能被中断。
  - 问题：i++就不是原子的（读-改-写三步）。
  - 保障：synchronized。

- 可见性(Visibility)
  - 定义：一个线程改了数据，其他线程能立马看见。
  - 问题：线程A改了flag=true，但还在自己缓存里没刷回去；线程B读主内存，还是false。
  - 保障：volatile,synchronized。

- 有序性(Ordering)
  - 定义：程序按照代码写的顺序执行。
  - 问题：编译器和CPU为了优化，会进行指令重排序(Instruction Reordering)。单线程没问题，多线程下可能会乱套（比如单例模式的双重检查锁）。
  - 保障：happens-before原则，使用volatile,synchronized来保障。

###  java多线程是什么？需要注意什么？







## 并发安全

### 介绍一下AQS