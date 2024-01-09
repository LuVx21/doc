<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [CountDownLatch](#countdownlatch)
- [Usage](#usage)
- [方法](#方法)
- [实现原理](#实现原理)
- [`CyclicBarrier` 比较](#cyclicbarrier-比较)
- [阅读](#阅读)

<!-- /TOC -->
</details>


## CountDownLatch

**作用**

允许一组线程全部执行完成后再继续执行当前线程, 即等待这一组线程全部执行结束

**使用场景**

`countDown`后可以直接继续线程内剩余逻辑, 不必等待其他未计数线程结束

如一个公司有十个人, 门卫需要等所有人全到了才能休息, 关键点在于每个人到了门卫直接放行, 进去这个人可以忙自己的事情


在使用时, 如果手动创建线程, 线程数目不能小于计数的值

## Usage

```Java
@Slf4j
public class CountDownLatchCase {
    private static final int            THREAD_COUNT_NUM = 10;
    private static final CountDownLatch countDownLatch   = new CountDownLatch(10);

    @AllArgsConstructor
    static class A implements Runnable {
        private int i;

        @SneakyThrows
        @Override
        public void run() {
            TimeUnit.MILLISECONDS.sleep(i * 100);
            // 每个线程结束后执行
            log.info("第{}个人到了, 线程:{}", i, Thread.currentThread().getName());
            countDownLatch.countDown();
            log.info("第{}个人进门, 线程:{}", i, Thread.currentThread().getName());
        }
    }

    private static void a() throws InterruptedException {
        for (int i = 0; i < THREAD_COUNT_NUM; i++) {
            new Thread(new A(i)).start();
        }
        // 所有线程结束后,倒计时完成开始执行
        countDownLatch.await();

        TimeUnit.SECONDS.sleep(1);
        log.info("10人全到了");
    }

    public static void main(String[] args) throws InterruptedException {
        a();
    }
}
```

## 方法

```Java
// 线程结束时手动执行, 计数器减一
public void countDown()
// 计数器没有到0前一直阻塞
public boolean await(long timeout, TimeUnit unit)
public void await() throws InterruptedException
```

## 实现原理

```Java
public CountDownLatch(int count) {
    if (count < 0) throw new IllegalArgumentException("count < 0");
    this.sync = new Sync(count);
}
private static final class Sync extends AbstractQueuedSynchronizer {
    Sync(int count) {
        setState(count);// 
    }

    int getCount() {
        return getState();
    }

    protected int tryAcquireShared(int acquires) {
        return (getState() == 0) ? 1 : -1;
    }

    protected boolean tryReleaseShared(int releases) {
        for (;;) {
            int c = getState();
            if (c == 0)
                return false;
            int nextc = c-1;
            if (compareAndSetState(c, nextc))
                return nextc == 0;
        }
    }
}
public void await() throws InterruptedException {
    sync.acquireSharedInterruptibly(1);// 阻塞
}
public void countDown() {
    sync.releaseShared(1);// 释放锁
}
public final boolean releaseShared(int arg) {
    if (tryReleaseShared(arg)) {
        doReleaseShared();
        return true;
    }
    return false;
}
```
继承AQS的共享锁来进行线程的通知,利用CAS来进行


## `CyclicBarrier` 比较

| CountDownLatch                                               | CyclicBarrier                                                |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 减计数方式                                                   | 加计数方式                                                   |
| 计算为0时释放所有等待的线程                                  | 计数达到指定值时释放所有等待线程                             |
| 计数为0时, 无法重置                                          | 计数达到指定值时, 计数置为0重新开始                          |
| 调用countDown()方法计数减一, 调用await()方法只进行阻塞, 对计数没任何影响 | 调用await()方法计数加1, 若加1后的值不等于构造方法的值, 则线程阻塞 |
| 不可重复利用                                                 | 可重复利用                                                   |

综合比较的话, 以接力赛场景为例, `CyclicBarrier`适合于第一棒吹哨阶段, 所有人做好准备(即`await()`)才能跑, `CountDownLatch`适合第二棒之后阶段, 接到棒(即`countDown()`)就可以跑

## 阅读

