---
layout: post
title: Java之TransferQueue
author: "gebaocai"
header-style: text
lang: zh
tags:
  - Java
  - TransferQueue
---
# 什么是TransferQueue?
TransferQueue是继承BlockingQueue的接口。
`BlockingQueue`是生产者等待消费者接收元素，而`TransferQueue`支持生产者等待消费者接收(`transfer(E)`)，也支持直接加入队列不需要消费者接收(`put(E)`)。`Non-blocking`和`timeout`的方式也可以通过`tryTransfer`来实现，还可以能过`hasWaitingConsumer()`查询是否有消费者等待元素，这和`peek`(检查是否有元素)是相反的操作。
# TransferQueue的实现类
- LinkedTransferQueue

LinkedTransferQueue实际上是ConcurrentLinkedQueue、SynchronousQueue（公平模式）和LinkedBlockingQueue的超集。而且LinkedTransferQueue更好用，因为它不仅仅综合了这几个类的功能，同时也提供了更高效的实现。  
Joe Bowbeer提供了一篇[William Scherer, Doug Lea, and Michael Scott](http://www.cs.rice.edu/~wns1/papers/2006-PPoPP-SQ.pdf)的论文，在这篇论文中展示了LinkedTransferQueue的算法，性能测试的结果表明它优于Java 5的那些类（译者注：ConcurrentLinkedQueue、SynchronousQueue和LinkedBlockingQueue）。LinkedTransferQueue的性能分别是SynchronousQueue的3倍（非公平模式）和14倍（公平模式）。因为像ThreadPoolExecutor这样的类在任务传递时都是使用SynchronousQueue，所以使用LinkedTransferQueue来代替SynchronousQueue也会使得ThreadPoolExecutor得到相应的性能提升。考虑到executor在并发编程中的重要性，你就会理解添加这个实现类的重要性了。  
Java 5中的SynchronousQueue使用两个队列（一个用于正在等待的生产者、另一个用于正在等待的消费者）和一个用来保护两个队列的锁。而LinkedTransferQueue使用CAS操作（译者注：参考[wiki](http://en.wikipedia.org/wiki/Compare-and-swap)）实现一个非阻塞的方法，这是避免序列化处理任务的关键。这篇论文还罗列了很多的细节和数据，如果你感兴趣，非常值得一读。