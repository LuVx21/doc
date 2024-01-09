<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [雪花算法](#雪花算法)
- [实现](#实现)
- [其他](#其他)
- [阅读](#阅读)

<!-- /TOC -->
</details>

## 雪花算法

![](https://gitee.com/LuVx/img/raw/master/algorithm/snowflake.png)

1 + 41 + 5 + 5 + 12 = 64bit

1. 1位始终为`0`, 起标识作用
2. 41位时间戳
3. 10位机器id, 高5bit是数据中心ID(`datacenter Id`), 低5bit是工作节点ID(`worker Id`), 最多可以容纳1024个节点
4. 12位序列号, 这个值在同一毫秒同一节点上从0开始不断累加, 最多可以累加到4095

优点:
1. 生成ID时不依赖于DB, 完全在内存生成, 高性能高可用.
2. ID呈趋势递增, 后续插入索引树的时候性能较好.

缺点:
1. 依赖于系统时钟的一致性

因此同一毫秒内, 最多可以生成 `1024 X 4096 = 4194304` 个唯一id

## 实现


```Java
public class SnowflakeIdWorker {

    //<editor-fold desc="Fields">
    /**
     * 开始时间截 (2020-01-01)
     */
    private final long start = 1577808000000L;

    /**
     * 上次生成ID的时间截
     */
    private long lastTimestamp = -1L;

    /**
     * 数据中心id所占的位数: 5
     * 机器id所占的位数: 5
     * 序列id位数: 12
     */
    private final long datacenterIdBits = 5L, workerIdBits = 5L, sequenceBits = 12L;

    /**
     * 数据中心ID(0~31)
     * 工作机器ID(0~31)
     * 毫秒内序列(0~4095)
     */
    private long datacenterId, workerId, sequence;

    /**
     * 机器ID向左移12位
     */
    private final long workerIdShift      = sequenceBits;
    /**
     * 数据标识id向左移17位(12+5)
     */
    private final long datacenterIdShift  = workerIdShift + workerIdBits;
    /**
     * 时间截向左移22位(5+5+12)
     */
    private final long timestampLeftShift = datacenterIdShift + datacenterIdBits;

    /**
     * 生成序列的掩码, 这里为4095 (0b111111111111=0xfff=4095)
     */
    private final long sequenceMask = -1L ^ (-1L << sequenceBits);

    //</editor-fold>

    //<editor-fold desc="Constructors">

    /**
     * 构造函数
     *
     * @param workerId     工作ID (0~31)
     * @param datacenterId 数据中心ID (0~31)
     */
    public SnowflakeIdWorker(long workerId, long datacenterId) {
        long maxDatacenterId = -1L ^ (-1L << datacenterIdBits);
        if (datacenterId < 0 || datacenterId > maxDatacenterId) {
            throw new IllegalArgumentException(String.format("datacenter Id can't be greater than %d or less than 0", maxDatacenterId));
        }
        long maxWorkerId = -1L ^ (-1L << workerIdBits);
        if (workerId < 0 || workerId > maxWorkerId) {
            throw new IllegalArgumentException(String.format("worker Id can't be greater than %d or less than 0", maxWorkerId));
        }
        this.workerId = workerId;
        this.datacenterId = datacenterId;
    }
    //</editor-fold>

    //<editor-fold desc="Methods">

    /**
     * 获得下一个ID (该方法是线程安全的)
     *
     * @return SnowflakeId
     */
    public synchronized long nextId() {
        long timestamp = timeGen();

        //如果当前时间小于上一次ID生成的时间戳, 说明系统时钟回退过这个时候应当抛出异常
        if (timestamp < lastTimestamp) {
            throw new RuntimeException(
                    String.format("Clock moved backwards. Refusing to generate id for %d milliseconds", lastTimestamp - timestamp));
        }

        //如果是同一时间生成的, 则进行毫秒内序列
        if (lastTimestamp == timestamp) {
            sequence = (sequence + 1) & sequenceMask;
            //毫秒内序列溢出
            if (sequence == 0) {
                //阻塞到下一个毫秒,获得新的时间戳
                timestamp = tilNextMillis(lastTimestamp);
            }
        } else {
            //时间戳改变, 毫秒内序列重置
            sequence = 0L;
        }

        //上次生成ID的时间截
        lastTimestamp = timestamp;

        //移位并通过或运算拼到一起组成64位的ID
        long id = ((timestamp - start) << timestampLeftShift)
                | (datacenterId << datacenterIdShift)
                | (workerId << workerIdShift)
                | sequence;
        log.info("生成id:{}", id);
        return id;
    }

    /**
     * 阻塞到下一个毫秒, 直到获得新的时间戳
     *
     * @param lastTimestamp 上次生成ID的时间截
     * @return 当前时间戳
     */
    protected long tilNextMillis(long lastTimestamp) {
        long timestamp = timeGen();
        while (timestamp <= lastTimestamp) {
            timestamp = timeGen();
        }
        return timestamp;
    }

    /**
     * 返回以毫秒为单位的当前时间
     *
     * @return 当前时间(毫秒)
     */
    protected long timeGen() {
        return System.currentTimeMillis();
    }
    //</editor-fold>
}
```

## 其他

美团: leaf

[Leaf——美团点评分布式ID生成系统](https://tech.meituan.com/2017/04/21/mt-leaf.html)

## 阅读

https://github.com/twitter-archive/snowflake
https://www.cnblogs.com/relucent/p/4955340.html
https://zhuanlan.zhihu.com/p/85837641