<details>
<summary>点击展开目录</summary>
<!-- TOC -->


<!-- /TOC -->
</details>

LockSupport是用来创建锁和其他同步类的基本线程阻塞原语

阻塞和唤醒的工具类

`park()`和`unpark()`作用是实现线程的阻塞和唤醒

```Java
public static void park(Object blocker) {
    Thread t = Thread.currentThread();
    setBlocker(t, blocker);
    UNSAFE.park(false, 0L);
    setBlocker(t, null);
}
public static void unpark(Thread thread) {
    if (thread != null)
        UNSAFE.unpark(thread);
}
```

可见其内部都是使用的`sun.misc.Unsafe`类进行操作的