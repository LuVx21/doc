---
title: 多线程
date: 2015-05-01
tags:
-
---
<!-- TOC -->

- [关于](#关于)
- [多线程的实现](#多线程的实现)
- [同步](#同步)
- [线程通信](#线程通信)
- [线程调度](#线程调度)
- [常用方法](#常用方法)
    - [Thread](#thread)
    - [Thread Instance](#thread-instance)
    - [Object](#object)
- [比较](#比较)
    - [sleep()和wait()方法的区别](#sleep和wait方法的区别)
    - [run()和Start()](#run和start)
    - [synchronize和ReentrantLock](#synchronize和reentrantlock)
    - [不推荐使用stop()和suspend()](#不推荐使用stop和suspend)

<!-- /TOC -->
# 关于

![生命周期](./img/threadlife.png)

# 多线程的实现


1:多线程有几种实现方案, 分别是哪几种?

* 继承Thread类,并重写run()方法,当创建子类的多个线程对象时, 其成员变量和run()方法中局部变量都是互不干扰的.
* 实现Runnable接口

> 扩展: 实现Callable接口.这个需要和线程池结合.

# 同步

同步有几种方式, 分别是什么?

* 同步代码块
* 同步方法

synchronized: 线程同步关键字

当一个线程A使用一个synchronized修饰的方法时, 其他线程想使用这个方法时就必须等待,
直到线程A 使用完该方法 (除非线程A使用wait主动让出CUP资源).
当被synchronized限定的代码段执行完, 就释放对象锁(信号量)

# 线程通信

一个线程在使用的同步方法中时, 可能根据问题的需要, 必须使用wait() (挂起)方法使本线程等待, 暂时让出CPU的使用权, 并允许其它线程使用这个同步方法.
其它线程如果在使用这个同步方法时不需要等待, 那么它用完这个同步方法的同时,
应当执行notify(), notifyAll()(恢复)方法通知所有的由于使用这个同步方法而处于等待的线程结束等待.

# 线程调度

每个Java线程都有一个优先级, 其范围都在1和10之间. 默认情况下, 每个线程的优先级都设置为5.

在线程创建之后的任何时刻都可以使用`setPriority(int priority)`来更改优先级.

假设某线程正在运行, 则只有出现以下情况之一, 才会使其暂停运行: 

* 一个具有更高优先级的线程变为就绪状态(Ready)；
* 由于输入/输出(或其他一些原因)、调用sleep、wait、yield方法使其发生阻塞；
* 对于支持时间分片的系统, 时间片的时间期满；

# 常用方法

## Thread

```Java
public static Thread currentThread(): 返回当前线程对象
public static void sleep(long millis): 使当前线程进入睡眠状态, 参数设定其等待时间, 不会释放锁
public static void yield(): 使当前线程放弃执行, 切换到其它线程
```
## Thread Instance

```Java
public void start(): 启动线程, JVM将调用此线程的run方法, 结果是将同时运行两个线程, 当前线程和执行run方法的线程.
public void run(): Thread的子类应该重写此方法, 内容应为该线程应执行的任务.
public void stop(): 停止线程运行, 并退出可执行状态. 【已过时】
public void resume(): 将暂停的线程继续执行.【已过时】
public void suspend(): 使线程暂停执行, 不退出可执行态.【已过时】
public void interrupt(): 中断线程.
public void join(): 在当前线程中加入调用join方法的线程A, 直到线程A死亡才能继续执行当前线程.
public void join(long millis): 在当前线程中加入调用join方法的线程A, 直到到达参数指定的毫秒数或线程A死亡才能继续执行当前线程.
public void setPriority(int newPriority): 设置线程优先级.
public void setDaemon(boolean on): 设置是否为后台线程.如果当前运行线程均为后台线程则JVM停止运行.该方法必须在start()方法之前使用.
public final void checkAccess(): 判断当前线程是否有权力修改调用此方法的线程.
public boolean isAlive(): 判断线程是否处于执行状态.返回值true表示处于运行状态, false表示已停止.
```
## Object

```Java
public void wait():  在其他线程调用此对象的 notify() 方法或 notifyAll() 方法前, 使当前线程进入等待状态, 会释放锁.
public void notify():  唤醒在此对象监视器上等待的单个线程.
public void notifyAll(): 唤醒在此对象监视器上等待的所有线程.
```

# 比较

## sleep()和wait()方法的区别

sleep():是Thread类的方法,使线程停止执行指定时间,因此必须指时间;不释放锁.
wait():Object类的方法,使进程进入等待状态,可以不指定时间, 也可以指定时间;释放锁.

两者在结束sleep或wait后都会重新进入就绪状态等待执行

## run()和Start()

启动一个线程是run()还是start()?它们的区别?

start():启动线程, 并由JVM自动调用run()方法
run():封装了被线程执行的代码,直接调用仅仅是普通方法的调用

5:为什么wait(),notify(),notifyAll()等方法都定义在Object类中

因为这些方法的调用是依赖于锁对象的, 而同步代码块的锁对象是任意锁.
而Object代码任意的对象, 所以, 定义在这里面.

## synchronize和ReentrantLock

synchronized和java.util.concurrent.locks.Lock的异同

还包括了中断锁等待和定时锁等待

在并发量小的时候, 用synchronize是比较好的选择, 并发量大的时候用Lock.
Lock有比synchronized更精确的线程语义和更好的性能.
synchronize是自动释放锁, Lock是主动释放锁,并且必须在finally从句中释放
Lock可以设定所等待的时间,
有些操作不会发生冲突现象, 需要用Lock解决, 比如同时读文件

> [参照](http://blog.csdn.net/maoyeqiu/article/details/46661719)

## 不推荐使用stop()和suspend()

stop()不安全: 它会解除由线程获取的所有锁定, 而且如果对象处于一种不连贯状态, 那么其他线程能在那种状态下检查和修改它们.结果很难检查出真正的问题所在. 
suspend()方法容易发生死锁: 调用suspend()的时候, 目标线程会停下来, 但却仍然持有在这之前获得的锁定.
此时, 其他任何线程都不能访问锁定的资源, 除非被“挂起”的线程恢复运行.对任何线程来说, 如果它们想恢复目标线程, 同时又试图使用任何一个锁定的资源, 就会造成死锁.所以不应该使用suspend(), 而应在自己的Thread类中置入一个标志, 指出线程应该活动还是挂起.若标志指出线程应该挂起, 便用 wait()命其进入等待状态.若标志指出线程应当恢复, 则用一个notify()重新启动线程