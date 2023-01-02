---
layout: post
title: Java之BlockingQueue
author: "gebaocai"
header-style: text
lang: zh
tags:
  - Java
  - BlockingQueue
---
# 什么是BlockingQueue?
BlockingQueue是继承Queue的接口。  
BlockingQueue在Queue的基础上增加了两个阻塞行为:当获取队列元素但队列为空时，会阻塞等侍队列中元素再返回;也支持增加元素时，如何队列已满，那么会等待队列有空间时再放入。
# BlockingQueue主要方法行为

|-----------------+---------+-------------+------------+------------|
|                 |  抛出异常   | 特殊值  | 阻塞  | 超时  |
|-----------------|:---------:|:-----------:|:----------:|:----------:|
| 插入 |add(e) | offer(e)      | put(e)    | offer(e, time, unit)    |
| 移除     |remove()         | poll()      | take()            | poll(time, unit)            |
| 检查      |elemnet()        | peek()             | 不适用            | 不适用            |
|=================+============+=================+================+================|

put(e)/take() 两方法，就是上节所提到的两个阻塞行为

BlockingQueue不接受`null`元素，`null`用来作为一个标记值指名`poll`操作失败。BlockingQueue可以是限制容量的，如果不指定容量，则会指定`Integer.MAX_VALUE`为默认值。主要应用于`producer-consumer`队列。

BlockingQueue是线程安全的，所有`queue`的方法都是通过内部锁或其它并发控制的形式来实现原子性效果。除非在实现时指定，否则批量`Collection`操作如:`addAll`,`containsAll`,`retainAll`和`removeAll`并不是原子操作。

# BlockingQueue主要实现类
- ArrayBlockingQueue

基于数据的有界队列，队列是按先进先出的原则，头部是进入时间最长的，尾部是进入时间最短的。新插入的元素放在队尾，获取元素从队列的头部取。
接收两个参数:
1. `capacity` 指明队列大小
2. `fair` 指明是否为公平锁，如为`true`则线程处理插入或删除时，按FIFO顺序处理。

- SynchronousQueue

实际上它不是一个真正的队列，因为它不会为队列中元素维护存储空间。
与其他队列不同的是，它维护一组线程，这些线程在等待着把元素加入或移出队列。
适用于直接交付工作， 而不是放入到一个队列里。
- LinkedBlockingQueue

基于连接节点可选边界的阻塞队列。
链接队列在并发应用程序中通常比基于数组的队列具有更高的吞吐量，
生产者端和消费者端分别采用了独立的锁来控制数据同步。
- PriorityBlockingQueue

由优先级支持的无界优先级队列，插入元素需实现Comparable接口，如下:
```
public static class Goods implements Comparable {
        private long createTime =  System.currentTimeMillis();

        public int compareTo(Object o) {
            return (this.createTime - ((Goods) o).createTime > 0) ? 1 : -1;
        }
    }
```
- DelayQueue

延迟元素的无界阻塞队列，其中的元素只能在其延迟过期时使用, 插入元素需实现Delayed接口，如下:
```
    public static class Goods implements Delayed {
        private long createTime =  System.currentTimeMillis();
        private long expirationDate = 10 * 1000;

        public long getDelay(TimeUnit unit) {
            long remain = createTime + expirationDate - System.currentTimeMillis();
            return unit.convert(remain, TimeUnit.MICROSECONDS);
        }

        public int compareTo(Delayed o) {
            return (this.createTime - ((Goods) o).createTime > 0) ? 1 : -1;
        }
    }
```

