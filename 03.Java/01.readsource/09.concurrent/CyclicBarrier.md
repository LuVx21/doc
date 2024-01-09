<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [CyclicBarrier](#cyclicbarrier)
- [Usage](#usage)
- [方法](#方法)
- [实现原理](#实现原理)
- [阅读](#阅读)

<!-- /TOC -->
</details>

## CyclicBarrier

**作用**

N个线程互相等待, 必须全部结束后才能继续后续操作

**使用场景**

`await`后不能直接继续线程内剩余逻辑, 必须等待所有的线程都结束才能继续

如一个公司有十个人, 门卫需要等所有人全到了才能休息, 关键点在于每个人到了门卫处必须等着, 等10个人全到了, 一起放行, 之后每个人可以忙自己的事情

可以看出`CyclicBarrier`不适合这个场景, 如果将员工换成百米跑运动员, 门卫换成吹哨裁判, 这个场景就适合了, 只有等10个人都准备好了才能吹哨

## Usage

```Java
@Slf4j
public class CyclicBarrierCase {
    private static final int           THREAD_COUNT_NUM  = 10;
    private static       CyclicBarrier callMasterBarrier = new CyclicBarrier(THREAD_COUNT_NUM, () -> {
        log.info("10人全到了, 开门放行");
        try {
            TimeUnit.SECONDS.sleep(2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    });

    @AllArgsConstructor
    static class A implements Runnable {
        private int i;

        @SneakyThrows
        @Override
        public void run() {
            TimeUnit.MILLISECONDS.sleep(i * 100);
            log.info("第{}个人到了, 线程:{}", i, Thread.currentThread().getName());
            callMasterBarrier.await();
            log.info("第{}个人进门, 线程:{}", i, Thread.currentThread().getName());
        }
    }

    private static void a() {
        for (int i = 1; i <= THREAD_COUNT_NUM; i++) {
            new Thread(new A(i)).start();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        a();
    }
}
```

## 方法

```Java
// 到达屏障前需完成的线程数
public CyclicBarrier(int parties)
// 指定到达屏障后的处理操作
public CyclicBarrier(int parties, Runnable barrierAction)
public int await()
public int await(long timeout, TimeUnit unit)
// 重置数据, 也是屏障可以重复使用的关键
public void reset()
```

## 实现原理


```Java
public CyclicBarrier(int parties, Runnable barrierAction) {
    if (parties <= 0) throw new IllegalArgumentException();
    this.parties = parties;
    this.count = parties;
    this.barrierCommand = barrierAction;
}

public int await() throws InterruptedException, BrokenBarrierException {
    try {
        return dowait(false, 0L);
    } catch (TimeoutException toe) {
        throw new Error(toe); // cannot happen
    }
}
private int dowait(boolean timed, long nanos)
    throws InterruptedException, BrokenBarrierException,
            TimeoutException {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        final Generation g = generation;

        if (g.broken)
            throw new BrokenBarrierException();

        if (Thread.interrupted()) {
            breakBarrier();
            throw new InterruptedException();
        }

        int index = --count;
        if (index == 0) {  // tripped
            boolean ranAction = false;
            try {
                final Runnable command = barrierCommand;
                if (command != null)
                    command.run();
                ranAction = true;
                nextGeneration();
                return 0;
            } finally {
                if (!ranAction)
                    breakBarrier();
            }
        }

        // loop until tripped, broken, interrupted, or timed out
        for (;;) {
            try {
                if (!timed)
                    trip.await();
                else if (nanos > 0L)
                    nanos = trip.awaitNanos(nanos);
            } catch (InterruptedException ie) {
                if (g == generation && ! g.broken) {
                    breakBarrier();
                    throw ie;
                } else {
                    // We're about to finish waiting even if we had not
                    // been interrupted, so this interrupt is deemed to
                    // "belong" to subsequent execution.
                    Thread.currentThread().interrupt();
                }
            }

            if (g.broken)
                throw new BrokenBarrierException();

            if (g != generation)
                return index;

            if (timed && nanos <= 0L) {
                breakBarrier();
                throw new TimeoutException();
            }
        }
    } finally {
        lock.unlock();
    }
}
```
利用ReentrantLock的Condition来阻塞和通知线程

## 阅读

