<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [作用](#作用)
- [Adder](#adder)
    - [使用场景](#使用场景)
    - [源码](#源码)
- [Accumulator](#accumulator)
    - [使用场景](#使用场景-1)
    - [源码](#源码-1)
- [其他](#其他)

<!-- /TOC -->
</details>

## 作用

Java8中新增了 `java.util.concurrent.atomic.Striped64` 类以及针对 Long 和 Double 两种数据类型给出的, 形似 `*Accumulator` 和 `*Adder` 的 4 个实现类

以下针对 Long 类型进行学习

## Adder

原子操作的数据增加运算

```Java
LongAdder adder = new LongAdder();
adder.increment();
adder.add(10);
System.out.println(adder);
```

### 使用场景

适用于多线程场景下的计数, 如使用map存储key出现了多少次

如:

```Java
ConcurrentHashMap<String, LongAdder> map = new ConcurrentHashMap<>(8);
String word = "aa";
map.computeIfAbsent(word, k -> new LongAdder()).increment();
// 或
map.getOrDefault(word, new LongAdder()).increment();
```

使用时指定增加的数值, 可通过`sum()`方法获取加法计算的结果, 累加的最终结果由两部分组成: `base`变量的值+cells数组中所有元素值的和

和 `AtomicLong` 对比, 其只有一个value, 所有cas操作都竞争这个变量, 性能会低, 而使用 LongAdder 发生CAS竞争时, 会根据当前线程找到cells的槽位, 粗略的可以认为是将增加值放在这个槽位中

### 源码

属性: `transient volatile Cell[] cells;`

是个数组, 本质上是个hash表, cas没有竞争时不会使用

设计上和 ThreadLocal 颇为相似, 都是将线程和绑定到具体的元素上

ThreadLocal 是将线程为map结构的key, Cell则是将线程绑定到数组中的元素上, 其元素的value属性包含了累加结果的一部分数值

属性: `transient volatile int cellsBusy;`

标志位, 用于对cells加锁(基于cas的机制), 0: 无锁, 1: 加锁, 创建cells时, 扩容时, 创建一个元素往数组中添加时这三种场景下加锁

加锁场景可在 `longAccumulate()` 中 搜索 `casCellsBusy()` 方法

属性: `transient volatile long base;`

存储计算结果(一部分结果)

改:

```Java
public void add(long x) {
    Cell[] cs; long b, v; int m; Cell c;
    // 只进行一次cas计算, 不是一个死循环
    if ((cs = cells) != null || !casBase(b = base, b + x)) {
        boolean uncontended = true;// 未发生竞争
        // 1. 数组为空
        // 2. 最大下标为负
        // 3. 计算出的对应位置元素是否为null
        // 4. 使用对应位置的元素进行加法运算(cas方式,无死循环)
        if (cs == null || (m = cs.length - 1) < 0 ||
            (c = cs[getProbe() & m]) == null ||
            !(uncontended = c.cas(v = c.value, v + x)))
            longAccumulate(x, null, uncontended);// 条件4执行到了且进入这里, uncontended=false 说明在对应槽位也发生了竞争
    }
}
// 获取当前线程的 `threadLocalRandomProbe` 属性值, 来自于java.lang.Thread#threadLocalRandomProbe,
// 修改时使用了java.util.concurrent.ThreadLocalRandom#PROBE对Thread的threadLocalRandomProbe属性进行了修改
// 结果会和最大下标进行与操作确定cells中对应槽位, 可以看出这个槽位和线程+下标有关
static final int getProbe() {
    return UNSAFE.getInt(Thread.currentThread(), PROBE);
}
final void longAccumulate(long x, LongBinaryOperator fn,
                            boolean wasUncontended) {
    int h;
    if ((h = getProbe()) == 0) {// 为0表示第一次进入该方法, 说明当前线程开始参与cell竞争
        ThreadLocalRandom.current(); // 在其中初始化threadLocalRandomProbe为1
        h = getProbe();
        wasUncontended = true;// 标记为未发生竞争
    }
    boolean collide = false; // 冲突标志, 表示当前线程对应的cells槽位发生冲突, true:有
    for (;;) {
        Cell[] as; Cell a; int n; long v;// 依次为数组, 元素, 数组长度, 加法之前的数值
        // 1. 数组不为null, 且含有元素
        if ((as = cells) != null && (n = as.length) > 0) {
            if ((a = as[(n - 1) & h]) == null) {// 对应槽位为null
                if (cellsBusy == 0) { // cells未加锁
                    Cell r = new Cell(x);   // 使用增加的值作为元素的属性
                    if (cellsBusy == 0 && casCellsBusy()) {// 标记为cells加锁
                        boolean created = false;// 标记元素是否加入成功
                        try {               // Recheck under lock
                            Cell[] rs; int m, j;
                            if ((rs = cells) != null &&
                                (m = rs.length) > 0 &&
                                rs[j = (m - 1) & h] == null) {
                                rs[j] = r;// 设置新元素
                                created = true;
                            }
                        } finally {
                            cellsBusy = 0;
                        }
                        if (created)
                            break;
                        continue;// 设置新元素失败, 死循环进行重试
                    }
                }
                collide = false;// 执行到此代码, 说明cellsBusy=1, 没有进入if分支, 标记为初始的无冲突进入新的循环使用
            }// 对应槽位不为null
            else if (!wasUncontended)// CAS失败(发生竞争)时进入
                wasUncontended = true;      // Continue after rehash, 扩容后进行重试
            // 对应位置有元素, cas进行加法计算, 成功直接退出, 可以推测, 在获取的时候需要找到这个槽位
            // fn为计算逻辑, 为空直接加, 不为空使用fn定义的计算逻辑, 在Accumulator类中发挥作用
            else if (a.cas(v = a.value, ((fn == null) ? v + x :
                                            fn.applyAsLong(v, x))))
                break;
            // 元素个数>=CPU个数或cells的执行改变(发生了扩容)
            else if (n >= NCPU || cells != as)
                collide = false;// 标记为初始的无冲突进入新的循环使用
            // 分支按顺序匹配, 至此说明有冲突, 标记为有冲突, 下次循环在下一个分支进行扩容
            else if (!collide)
                collide = true;
            // 进行扩容
            else if (cellsBusy == 0 && casCellsBusy()) {
                try {
                    if (cells == as) {// 检查是否已扩容
                        Cell[] rs = new Cell[n << 1];// 原来的2倍
                        for (int i = 0; i < n; ++i)
                            rs[i] = as[i];
                        cells = rs;
                    }
                } finally {
                    cellsBusy = 0;
                }
                collide = false;// 标记为初始的无冲突进入新的循环使用
                continue;                   // Retry with expanded table
            }
            h = advanceProbe(h);// 重新计算hash值
        }
        // 2. (cells 为null 或 无元素) + cells未加锁 + cells指向未发生变更(创建/扩容) + 加锁成功 -> 进入分支
        else if (cellsBusy == 0 && cells == as && casCellsBusy()) {
            boolean init = false;
            try {                           // Initialize table
                if (cells == as) {
                    Cell[] rs = new Cell[2];// 新建cells数组
                    rs[h & 1] = new Cell(x);
                    cells = rs;// 设置指向
                    init = true;
                }
            } finally {
                cellsBusy = 0;// 解锁
            }
            if (init)// 初始化数组成功
                break;
        }
        // 3. 以上均无效时, 再次尝试累加到base属性上
        else if (casBase(v = base, ((fn == null) ? v + x :
                                    fn.applyAsLong(v, x))))
            break;                          // Fall back on using base
    }
}
```

查:

```Java
public long sum() {
    Cell[] as = cells; Cell a;
    long sum = base;// 这个可能是计算后的结果, 也可能是计算后的结果的一部分
    if (as != null) {
        for (int i = 0; i < as.length; ++i) {// 遍历整个数组(数组中存储的是发生CAS竞争时的增加量)
            if ((a = as[i]) != null)
                sum += a.value;// 因为改部分是使用增加的值作为元素的属性
        }
    }
    return sum;
}
```

## Accumulator

原子操作的数据运算, 运算类型可以通过 `LongBinaryOperator` 类型的参数进行指定

```Java
LongAccumulator accumulator = new LongAccumulator(
        (l, r) -> l + r, 1001
);
accumulator.accumulate(10);
System.out.println(accumulator);
```

### 使用场景

同 LongAdder 类似, 但支持更多的, 自定义的计算类型

使用时候指定计算的逻辑和计算的原始值, 每次操作时候, 使用前次的计算结果(首次使用原始值)通过计算逻辑进行计算

### 源码

属性: `private final long identity;`

原始值

属性: `transient volatile long base;`

计算后的结果值(一部分)


关键代码基本和 LongAdder 类似, 对比可以发现, 中间多了使用自定义逻辑计算结果的过程

改:

```Java
public void accumulate(long x) {
    Cell[] as; long b, v, r; int m; Cell a;
    if ((as = cells) != null ||
        (r = function.applyAsLong(b = base, x)) != b && !casBase(b, r)) {
        boolean uncontended = true;
        if (as == null || (m = as.length - 1) < 0 ||
            (a = as[getProbe() & m]) == null ||
            !(uncontended = (r = function.applyAsLong(v = a.value, x)) == v
                || a.cas(v, r)))
            longAccumulate(x, function, uncontended);
    }
}
```

查:

```Java
public long get() {
    Cell[] as = cells; Cell a;
    long result = base;
    if (as != null) {
        for (int i = 0; i < as.length; ++i) {
            if ((a = as[i]) != null)
                result = function.applyAsLong(result, a.value);
        }
    }
    return result;
}
```

## 其他

**`ThreadLocalRandom`类的作用**

`Striped64`的源码中多次使用到`ThreadLocalRandom` 类的 `PROBE` 变量, 并使用到了以下方法

```Java
public static ThreadLocalRandom current() {
    if (UNSAFE.getInt(Thread.currentThread(), PROBE) == 0)
        localInit();
    return instance;
}
static final void localInit() {
    int p = probeGenerator.addAndGet(PROBE_INCREMENT);
    int probe = (p == 0) ? 1 : p; // skip 0
    long seed = mix64(seeder.getAndAdd(SEEDER_INCREMENT));
    Thread t = Thread.currentThread();
    UNSAFE.putLong(t, SEED, seed);
    UNSAFE.putInt(t, PROBE, probe);
}
```

这个类是一个与当前线程隔离的随机数生成器, 通过为每个线程实例化一个随机数生成器, 来减少系统开销和对资源的争用

在 Java7 中添加, 用以解决 `Random` 类在并发场景下的性能问题

调用 `current()` 方法主要是为了初始化随机数生成器, 生成`PROBE` 变量的值, 从而确定 cell 的位置
