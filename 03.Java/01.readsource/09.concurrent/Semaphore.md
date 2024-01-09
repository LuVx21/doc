<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [Semaphore](#semaphore)
- [Usage](#usage)
- [实现原理](#实现原理)
- [阅读](#阅读)

<!-- /TOC -->
</details>

## Semaphore

**作用**

控制访问特定资源的线程数目

可用于流量控制, 限制最大的并发访问数

也可用于多个共享资源的互斥使用

**使用场景**

m个银行客户到n个窗口的银行办理业务的例子

## Usage

```Java
@Slf4j
public class SemaphoreCase {
    /**
     * 客户数
     */
    private static final int       num       = 10;
    /**
     * 银行窗口数
     */
    private static final int       n1        = 3;
    private static final Semaphore semaphore = new Semaphore(n1);

    private static final ExecutorService executor = Executors.newFixedThreadPool(100);

    @SneakyThrows
    public void run(int i) {
        try {
            semaphore.acquire();
            log.info("第{}个人业务start...", i);
            TimeUnit.SECONDS.sleep(2);
            log.info("第{}个人业务end...", i);
        } finally {
            semaphore.release();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        SemaphoreCase exec = new SemaphoreCase();
        IntStream.rangeClosed(1, num).forEach(i ->
                executor.execute(() -> exec.run(i))
        );
        executor.shutdown();
    }
}
```

## 实现原理

```Java
// 许可线程的数量
public Semaphore(int permits) {
    sync = new NonfairSync(permits);
}
public Semaphore(int permits, boolean fair) {
    sync = fair ? new FairSync(permits) : new NonfairSync(permits);
}
public void acquire() throws InterruptedException {
    sync.acquireSharedInterruptibly(1);
}
public void release() {
    sync.releaseShared(1);
}
```
还有`acquire(int)` 和 `release(int)`方法, 参数的意思可以理解为1个人占用几个窗口办业务, 但内部调用的方法都是一样

`AbstractQueuedSynchronizer`

## 阅读


