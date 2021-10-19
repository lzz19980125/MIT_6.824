### 6.824

分布式系统的优点：1.并行管理大量CPU内存  2. fault tolerance  使用两台计算机做完全相同的任务以防止其中一台出现问题 3.克服物理空间距离的通信问题  4.安全问题

infrastructure：

storage：

communications：

computation：mapreduce or something

RPC（remote procedure call）远程过程调用：RPC的目标是试图掩盖我们正在不可靠网络上通信的事实

threads:线程：让我们可以驾驭多核计算机，在本门课程中，线程更加提供了一种结构化的并发操作方式，为的是程序员能够简化并发操作

并发（concurrency）控制，如：锁（lock）

performance或者叫throughput（吞吐量）：人们最多提到的是scalable（可扩展性）：简单来讲当我能用一台电脑去解决这一问题，如果我多用一台电脑，则解决问题的时间将减少一半，这就叫scalable。

fault tolerance： 一个关键的概念是availability（可用性），一台电脑很少出问题，然而在一个分布式系统当中，当数千台电脑在一起工作时，产生的错误概率将会大大提高，这是就需要你设计的分布式系统具有很强的容错性，或者说是高可用性。另一个概念是recoverability，就是说在出现错误时，系统只是不在响应，即停止运行，在进行修复之后，系统可以恢复正常使用，而不是在发生错误时，系统产生了更坏的结果，即不损失正确性（recoverability：可恢复性，这个概念要比高可用性弱一些）

实现fault tolerance一个重要的手段是replication(复制)

consistency（一致性）：Put(key,value) ; Get(key) ——>value.  在单机系统中，通常只有一个服务器和一张表（key——value system），因此当你使用put和get命令时，直接更新就好了，并不容易出错；但在分布式系统，当我们系统采用replication去提高系统的容错率时，此时会有很多版本的table，此时你再采用put和get命令，应该如何处理呢？这个问题表现为分布式系统中数据的一致性（consistency）

## Map reduce



