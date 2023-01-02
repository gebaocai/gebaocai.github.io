---
layout: post
title: Java之ThreadPoolExecutor
author: "gebaocai"
header-style: text
lang: zh
tags:
  - Java
  - ThreadPoolExecutor
  - 线程池
---
# 为什么需要线程池?
我们有两种常见的创建线程的方法，一种是继承Thread类，一种是实现Runnable的接口，Thread类其实也是实现了Runnable接口。但是我们创建这两种线程在运行结束后都会被虚拟机销毁，如果线程数量多的话，频繁的创建和销毁线程会大大浪费时间和效率，更重要的是浪费内存。那么有没有一种方法能让线程运行完后不立即销毁，而是让线程重复使用，继续执行其他的任务哪？

这就是线程池的由来，很好的解决线程的重复利用，避免重复开销。
# 线程池的优点
1、线程是稀缺资源，使用线程池可以减少创建和销毁线程的次数，每个工作线程都可以重复使用。

2、可以根据系统的承受能力，调整线程池中工作线程的数量，防止因为消耗过多内存导致服务器崩溃。
# 线程池状态
RUNNING 创建线程池后的状态
SHUTDOWN 调用shutdown后的状态，线程池不接受新的任务，等待缓冲队列中的任务执行完闭。
STOP  调用shotdownNow后的状态，线程池不接受新的任务，并尝试终止正在执行的任务。
TERMINATED 当线程池处于SHUTDOWN或STOP后，工作线程已经销毁，任务缓冲队列已销毁或已执行结束后，状态设置为TERMINATED.
# 四种线程池的实现
首先看一下ThreadPoolExecutor的构造函数
```
public ThreadPoolExecutor(int corePoolSize,
                  int maximumPoolSize,
                  long keepAliveTime,
                  TimeUnit unit,
                  BlockingQueue<Runnable> workQueue,
                  ThreadFactory threadFactory,
                  RejectedExecutionHandler handler)
Creates a new ThreadPoolExecutor with the given initial parameters.
Parameters:
  corePoolSize - 线程池维持的core线程数
  maximumPoolSize - 线程池能维持的最大线程数
  keepAliveTime - 超过idel线程数的线程最长保活时间
  unit - keepAliveTime的时间单位
  workQueue - 任务缓存队列.
  threadFactory - the factory to use when the executor creates a new thread
  handler - the handler to use when execution is blocked because the thread bounds and queue capacities are reached
Throws:
  IllegalArgumentException - if one of the following holds:
  corePoolSize < 0
  keepAliveTime < 0
  maximumPoolSize <= 0
  maximumPoolSize < corePoolSize
  NullPointerException - if workQueue or threadFactory or handler is null
```

**newCachedThreadPool**

可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。
```
    public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }
```
**newSingleThreadExecutor**
创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务
```
    public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }
```
**newFixedThreadPool**

创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。
```
    public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }
```
**newScheduledThreadPool**

创建一个定长线程池，支持定时及周期性任务执行。
```
    public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
        return new ScheduledThreadPoolExecutor(corePoolSize);
    }

    public ScheduledThreadPoolExecutor(int corePoolSize) {
        super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
              new DelayedWorkQueue());
    }
```
有两种任务提交方式:

1.scheduleAtFixedRate

按任务开始时间周期性任务执行

2.scheduleWithFixedDelay

按任务结束时间延迟定长时间执行任务
