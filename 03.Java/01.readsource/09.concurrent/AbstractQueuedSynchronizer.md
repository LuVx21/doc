<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [AQS](#aqs)
- [CLH同步队列](#clh同步队列)
- [独占式](#独占式)
    - [获取](#获取)
    - [响应中断获取](#响应中断获取)
    - [超时获取](#超时获取)
    - [释放](#释放)
- [共享式](#共享式)
    - [获取](#获取-1)
    - [响应中断获取](#响应中断获取-1)
    - [超时获取](#超时获取-1)
    - [释放](#释放-1)
- [阻塞与释放](#阻塞与释放)

<!-- /TOC -->
</details>


# AQS

```Java
// CLH队列的头节点
private transient volatile Node head;
// CLH队列的尾节点
private transient volatile Node tail;
// 同步状态
private volatile int state;
```

`tryAcquire, tryRelease`,
`tryAcquireShared, tryReleaseShared`,
`isHeldExclusively`

子类可能实现的方法

# CLH同步队列

名称来自jdk的相关源码的作者`Craig, Landin 和 Hagersten`

AQS通过内置的FIFO双向同步队列来完成资源获取线程的排队工作, 头节点即是当前正在执行的节点,
如果当前线程获取同步状态(锁)失败时, AQS则会将当前线程以及等待状态等信息构造成一个节点并将其加入同步队列, 并阻塞当前线程,
当同步状态释放时, 则会把头节点中的线程唤醒, 使其再次尝试获取同步状态.

队列节点定义:
```Java
static final class Node {
    static final Node SHARED = new Node();
    static final Node EXCLUSIVE = null;
    /**
     * 因为超时或者中断, 节点会被设置为取消状态, 被取消的节点不会参与到竞争中的, 会一直保持取消状态不会转变为其他状态
     */
    static final int CANCELLED =  1;
    /**
     * 后继节点的线程处于等待状态, 而当前节点的线程如果释放了同步状态或者被取消, 将会通知后继节点, 使后继节点的线程得以运行
     * 表示当前节点的后继节点包含的线程需要运行
     */
    static final int SIGNAL    = -1;
    /**
     * 节点在等待队列中, 节点线程等待在Condition上, 当其他线程对Condition调用了signal()后, 该节点将会从等待队列中转移到同步队列中, 加入到同步状态的获取中
     * 表示当前节点在等待 condition
     */
    static final int CONDITION = -2;
    /**
     * 仅在线程处在SHARED时使用
     * 表示下一次共享式同步状态获取将会无条件地传播下去
     */
    static final int PROPAGATE = -3;
    /** 当前节点在队列中的状态, 默认为0(Node被初始化时) */
    volatile int waitStatus;
    volatile Node prev;// 前驱指针
    volatile Node next;// 后继指针
    volatile Thread thread;// 处于该节点的线程
    Node nextWaiter;// 指向下一个处于CONDITION状态的节点

    final boolean isShared() {
        return nextWaiter == SHARED;
    }

    final Node predecessor() throws NullPointerException {
        Node p = prev;// 返回前驱节点, 没有的话抛出NPE
        if (p == null)
            throw new NullPointerException();
        else
            return p;
    }

    Node() {
    }

    Node(Thread thread, Node mode) {
        this.nextWaiter = mode;
        this.thread = thread;
    }

    Node(Thread thread, int waitStatus) {
        this.waitStatus = waitStatus;
        this.thread = thread;
    }
}
```

入队列:
```Java
private Node addWaiter(Node mode) {
    Node node = new Node(Thread.currentThread(), mode);
    Node pred = tail;
    if (pred != null) {
        node.prev = pred;
        // 尝试设置一次
        if (compareAndSetTail(pred, node)) {
            pred.next = node;
            return node;
        }
    }
    // 上面的失败, 死循环设置
    enq(node);// 尾节点为空或尾节点已经改变
    return node;
}
private Node enq(final Node node) {
    for (;;) {
        Node t = tail;
        if (t == null) {
            if (compareAndSetHead(new Node()))// 队列无元素, 创建一个头节点
                tail = head;
        } else {
            node.prev = t;
            if (compareAndSetTail(t, node)) {
                t.next = node;
                return t;
            }
        }
    }
}
```

可以看出, 头节点是一个无参构造函数创建的节点

出队列:
参考线程唤醒部分


# 独占式

## 获取

在AQS中维护着一个FIFO的同步队列, 当线程获取同步状态失败后, 则会加入到这个CLH同步队列的对尾并一直保持着自旋.
在CLH同步队列中的线程在自旋时会判断其前驱节点是否为首节点, 如果为首节点则不断尝试获取同步状态, 获取成功则退出CLH同步队列.
当线程执行完逻辑后, 会释放同步状态, 释放后会唤醒其后继节点.

当前线程获取同步状态失败时, 会封装当前线程以及等待状态等信息为节点, 然后将其加入CLH同步队列, 并阻塞线程.

但实际上, 获取失败并不是立即阻塞, 而是判断是否需要阻塞

```Java
// 获取失败且需要阻塞,则中断
public final void acquire(int arg) {
    if (!tryAcquire(arg) &&
        acquireQueued(addWaiter(Node.EXCLUSIVE), arg))// 获取锁失败, 加入等待队列
        selfInterrupt();
}
final boolean acquireQueued(final Node node, int arg) {
    boolean failed = true;// 是否成功获取资源
    try {
        boolean interrupted = false;// 是否中断过
        for (;;) {// 循环结束: 前驱节点是头结点, 且获取锁成功
            final Node p = node.predecessor();// 前驱节点
            // 如果是头节点的直接后继, 则不阻塞并成为头节点
            if (p == head && tryAcquire(arg)) {
                setHead(node);// 获取成功, 头指针指向当前节点
                p.next = null; // help GC
                failed = false;
                return interrupted;
            }
            // 不是
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {// 线程被挂起后, 循环结束, 进入finally
        if (failed)// 设置状态为取消
            cancelAcquire(node);
    }
}
```

## 响应中断获取

```Java
public final void acquireInterruptibly(int arg)
        throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    if (!tryAcquire(arg))
        doAcquireInterruptibly(arg);
}
private void doAcquireInterruptibly(int arg)
    throws InterruptedException {
    final Node node = addWaiter(Node.EXCLUSIVE);
    boolean failed = true;
    try {
        for (;;) {
            final Node p = node.predecessor();
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return;
            }
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                throw new InterruptedException();
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

## 超时获取

在支持响应中断的基础上继续增强, 支持超时机制

当前线程在指定时间内没能获取到同步状态, 则会返回false

```Java
public final boolean tryAcquireNanos(int arg, long nanosTimeout)
        throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    return tryAcquire(arg) ||
        doAcquireNanos(arg, nanosTimeout);
}
private boolean doAcquireNanos(int arg, long nanosTimeout)
        throws InterruptedException {
    if (nanosTimeout <= 0L)
        return false;
    final long deadline = System.nanoTime() + nanosTimeout;
    final Node node = addWaiter(Node.EXCLUSIVE);
    boolean failed = true;
    try {
        for (;;) {
            final Node p = node.predecessor();
            if (p == head && tryAcquire(arg)) {
                setHead(node);
                p.next = null; // help GC
                failed = false;
                return true;
            }
            nanosTimeout = deadline - System.nanoTime();// 计算出需休眠的时间
            if (nanosTimeout <= 0L)// 需要休眠为负, 说明此时已经超过deadline
                return false;
            if (shouldParkAfterFailedAcquire(p, node) &&
                nanosTimeout > spinForTimeoutThreshold)// 大于1000毫秒才休眠,否则时间太短不休眠
                LockSupport.parkNanos(this, nanosTimeout);
            if (Thread.interrupted())
                throw new InterruptedException();
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

## 释放

```Java
public final boolean release(int arg) {
    if (tryRelease(arg)) {// 锁没被任何线程持有
        Node h = head;
        // 释放头节点(非空且参与竞争)
        if (h != null && h.waitStatus != 0)
            unparkSuccessor(h);
        return true;
    }
    return false;
}
```

# 共享式

共享式与独占式的最主要区别在于同一时刻独占式只能有一个线程获取同步状态, 而共享式在同一时刻可以有多个线程获取同步状态.

例如读操作可以有多个线程同时进行, 而写操作同一时刻只能有一个线程进行写操作, 其他操作都会被阻塞.

## 获取

```Java
public final void acquireShared(int arg) {
    if (tryAcquireShared(arg) < 0)
        doAcquireShared(arg);
}
private void doAcquireShared(int arg) {
    final Node node = addWaiter(Node.SHARED);
    boolean failed = true;
    try {
        boolean interrupted = false;
        for (;;) {
            final Node p = node.predecessor();
            if (p == head) {
                int r = tryAcquireShared(arg);
                if (r >= 0) {
                    setHeadAndPropagate(node, r);
                    p.next = null; // help GC
                    if (interrupted)
                        selfInterrupt();
                    failed = false;
                    return;
                }
            }
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                interrupted = true;
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

## 响应中断获取

```Java
public final void acquireSharedInterruptibly(int arg)
        throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    if (tryAcquireShared(arg) < 0)
        doAcquireSharedInterruptibly(arg);
}
private void doAcquireSharedInterruptibly(int arg)
    throws InterruptedException {
    final Node node = addWaiter(Node.SHARED);
    boolean failed = true;
    try {
        for (;;) {
            final Node p = node.predecessor();
            if (p == head) {
                int r = tryAcquireShared(arg);
                if (r >= 0) {
                    setHeadAndPropagate(node, r);
                    p.next = null; // help GC
                    failed = false;
                    return;
                }
            }
            if (shouldParkAfterFailedAcquire(p, node) &&
                parkAndCheckInterrupt())
                throw new InterruptedException();
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

## 超时获取

```Java
public final boolean tryAcquireSharedNanos(int arg, long nanosTimeout)
        throws InterruptedException {
    if (Thread.interrupted())
        throw new InterruptedException();
    return tryAcquireShared(arg) >= 0 ||
        doAcquireSharedNanos(arg, nanosTimeout);
}
private boolean doAcquireSharedNanos(int arg, long nanosTimeout)
        throws InterruptedException {
    if (nanosTimeout <= 0L)
        return false;
    final long deadline = System.nanoTime() + nanosTimeout;
    final Node node = addWaiter(Node.SHARED);
    boolean failed = true;
    try {
        for (;;) {
            final Node p = node.predecessor();
            if (p == head) {
                int r = tryAcquireShared(arg);
                if (r >= 0) {
                    setHeadAndPropagate(node, r);
                    p.next = null; // help GC
                    failed = false;
                    return true;
                }
            }
            nanosTimeout = deadline - System.nanoTime();
            if (nanosTimeout <= 0L)
                return false;
            if (shouldParkAfterFailedAcquire(p, node) &&
                nanosTimeout > spinForTimeoutThreshold)
                LockSupport.parkNanos(this, nanosTimeout);
            if (Thread.interrupted())
                throw new InterruptedException();
        }
    } finally {
        if (failed)
            cancelAcquire(node);
    }
}
```

## 释放

```Java
public final boolean releaseShared(int arg) {
    if (tryReleaseShared(arg)) {
        doReleaseShared();
        return true;
    }
    return false;
}
private void doReleaseShared() {
    for (;;) {
        Node h = head;
        if (h != null && h != tail) {
            int ws = h.waitStatus;
            if (ws == Node.SIGNAL) {
                if (!compareAndSetWaitStatus(h, Node.SIGNAL, 0))
                    continue;
                unparkSuccessor(h);
            }
            else if (ws == 0 &&
                        !compareAndSetWaitStatus(h, 0, Node.PROPAGATE))
                continue;
        }
        if (h == head)
            break;
    }
}
```

# 阻塞与释放

在线程获取同步状态时如果获取失败, 则加入CLH同步队列, 通过通过自旋的方式不断获取同步状态, 但是在自旋的过程中则需要判断当前线程是否需要阻塞

```Java
if shouldParkAfterFailedAcquire(p, node) && parkAndCheckInterrupt()
if shouldParkAfterFailedAcquire(p, node) && nanosTimeout > spinForTimeoutThreshold)
```

以上就是这个判断并阻塞的逻辑

```Java
private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
    int ws = pred.waitStatus;// 前驱节点的状态
    if (ws == Node.SIGNAL)// 前驱节点处于唤醒状态
        return true;
    if (ws > 0) {// 已取消(等待超时或中断), 删除不参与竞争的线程
        do {
            node.prev = pred = pred.prev;
        } while (pred.waitStatus > 0);
        pred.next = node;
    } else {// 0, -2, -3时
        compareAndSetWaitStatus(pred, ws, Node.SIGNAL);// 唤醒前驱节点
    }
    return false;
}
```

`shouldParkAfterFailedAcquire`会判断前置节点的状态来判断是否要将当前线程挂起,

而由`LockSupport.park(this);`或`LockSupport.parkNanos(this, nanosTimeout);`实施挂起操作

当线程释放同步状态后, 则需要唤醒该线程的后继节点, 这点在`release()`方法中体现, 使用`unparkSuccessor()`唤醒

```Java
// 参数往往是头节点
private void unparkSuccessor(Node node) {
    int ws = node.waitStatus;
    // 参与竞争的线程
    if (ws < 0)
        compareAndSetWaitStatus(node, ws, 0);
    Node s = node.next;
    if (s == null || s.waitStatus > 0) {// 节点为null或者已取消状态
        s = null;
        // 从尾节点往前找, 找到node后第一个参与竞争的节点
        for (Node t = tail; t != null && t != node; t = t.prev)
            if (t.waitStatus <= 0)
                s = t;
    }
    // 线程唤醒
    if (s != null)
        LockSupport.unpark(s.thread);
}
```
