<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [随机数](#随机数)
- [Sealed Classes](#sealed-classes)
- [Vector API](#vector-api)

<!-- /TOC -->
</details>

## 随机数

引入RandomGenerator及RandomGeneratorFactory提供更好的随机数生成

```java
RandomGenerator generator = RandomGeneratorFactory.all()
    .filter(RandomGeneratorFactory::isJumpable)
    .filter(factory -> factory.stateBits() > 128)
    .findAny()
    .map(RandomGeneratorFactory::create)
//  if you need a `JumpableGenerator`:
//  .map(JumpableGenerator.class::cast)
    .orElseThrow();
```

## Sealed Classes

## Vector API

用于矢量计算