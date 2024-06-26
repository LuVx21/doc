<details>
<summary>点击展开目录</summary>

- [BloomFilter](#bloomfilter)
- [流程](#流程)
- [使用](#使用)
- [阅读](#阅读)

</details>

## BloomFilter

布隆过滤器

* 高效地插入和查询
* 占用空间更少
* 缺点是其返回的结果是概率性的, 而不是确切的, 即最终判断的结果是: 一定不存在或者可能存在

有hash函数个数k, 位数组长度m, 插入元素个数n, 误报率p这样4个因素

符合业务的4因素的设置公式为:

`m = -(n * lnp) / (ln2 * ln2)`

`k = (m / n) * ln2`

应用场景:
1. 判断一个元素是否在一个大集合中
2. 解决缓存穿透问题
3. 判断一个元素(ip, 邮箱)是否在黑名单中
4. 根据requestid进行幂等实现

## 流程

![](https://gitee.com/LuVx/img/raw/master/redis/redis_bloom_filter.png)

x,y,z三个元素, 每个元素都经过了3个hash函数, 确定了位数组的三个位置

1. 确定位数组
2. 基于hash函数对元素进行计算得到hash值, 多个hash函数得到多个hash值
3.

其应用中有3个主体: a个元素, b个hash函数, 长度为c的位数组(初始时所有元素值为0), 其中hash函数的结果在位数组的范围内

以黑名单为类:

想要验证一个IP是否位于黑名单中, 首先要有一个黑名单, 然后需要将黑名单的所有元素映射到bitmap中,

每个元素经过一个hash函数都对得到一个位数组的位置, 然后将对应位置的值设为1, b个函数理论上会将b个位置设为1, 如果`a*b`个结果有相同, 该位置的值只能会是1, 无论相同多少次

当判断一个ip地址, 经过b个哈希hash函数后, 确定了b个位置, 对这些位置的值进行与操作, 为1则存在, 否则不存在

可以看到, 如果一个ip在黑名单内, 结果一定为1, 但如果不在, 也可能为1, 因为hash函数可能会碰撞的, 两个不同的ip, 经过同一个hash函数后得到的结果相同

总结一下就是返回1,判定为存在, 有可能是误判, 但如果返回0, 判定为不存在, 那么一定是不存在, 至少有一个hash函数的结果位置没有设置为1

因此有可能将正常的ip认为在黑名单内加以处理, 所以遇到1的结果时, 只能认为可能在黑名单内

> 一定范围内, 和位数组的长度正相关, 准确率和哈希函数个数正相关(但过多会导致更多的位置被置为1), 和哈希函数碰撞率负相关(取决于hash函数的均匀性)

## 使用

JDK方式

基于 `java.util.BitSet` 实现, 关键点在于多个hash函数的实现


Guava方式

```Java
BloomFilter<String> filter = BloomFilter.create(
        Funnels.stringFunnel(StandardCharsets.UTF_8),
        100,
        0.01);
System.out.println(filter.mightContain("foo"));
filter.put("foo");
System.out.println(filter.mightContain("foo"));
```

Redis方式

Redis有`setbit`和`getbit`命令, 且内存性能优秀, 是作为布隆过滤器使用的好工具

阅读[BloomFilter(大数据去重)+Redis(持久化)策略](https://blog.csdn.net/qq_18495465/article/details/78500472)

除了以上比较基础的实现方式, redis本身是支持模块功能的

在[模块官网](https://redis.io/modules)中推荐了布隆过滤器Module: [RedisBloom](https://github.com/RedisBloom/RedisBloom)

常见命令如下:

* `bf.add`: 添加1个元素进过滤器. `BF.ADD {key} {item}`.
* `bf.madd`: 添加不定量个元素进过滤器. `BF.MADD {key} {item} [item ...]`
* `bf.exists`: 判断1元素是否存在. `BF.EXISTS {key} {item}`
* `bf.mexists`: 判断不定量个元素是否存在: `BF.MEXISTS {key} {item} [item ...]`
* `bf.reserve`: 可以指定误报率和元素容量的过滤器, 可以指定扩展因子expansion用于指定子过滤器的大小, `BF.RESERVE {key} {error_rate} {capacity} [EXPANSION expansion]`

> 命令中的key就是过滤器的名称

## 阅读
