<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [流](#流)
- [基本类型Stream](#基本类型stream)
- [方法](#方法)

<!-- /TOC -->
</details>


## 流

BaseStream


## 基本类型Stream


在Java 8的Stream API中提供了`of`方法, 如果传入基本类型的参数, 内部其实得到的是一个引用类型的Stream

专门针对基本类型优化的API

如:

`IntStream`, `LongStream`, `DoubleStream`

```Java
Stream<Integer> stream = Stream.of(1, 2, 3);
IntStream intStream = IntStream.of(1, 2, 3);
// 开区间 1~9
IntStream.range(1, 10);
// 闭区间 1~10
IntStream.rangeClosed(1, 10);
// 无限个 1
IntStream.generate(() -> 1);
// 无限的奇数
IntStream.iterate(1, operand -> operand + 2);
```

```Java
int[] array = {1, 2, 3, 4, 5};
IntStream arrayStream = Arrays.stream(array);
// 开区间 下标 2~4(不含)
IntStream rangeArray = Arrays.stream(array, 2, 4);
```

## 方法

filter
map
mapToObj
mapToLong
mapToDouble
min
summaryStatistics: 汇总方法, 主要是对流中的数据进行聚合运算
