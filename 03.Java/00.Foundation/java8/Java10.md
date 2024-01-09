<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [集合](#集合)
- [GC](#gc)
- [其他](#其他)

<!-- /TOC -->
</details>

## 集合

新增以下方法可用于创建不可变集合:
```Java
List.copyOf(Collection<? extends E> coll)
Set.copyOf(Collection<? extends E> coll)
Map.copyOf(Map<? extends K, ? extends V> map)
```

Collectors中新增了以下方法用于创建不可变集合:
```Java
Collectors.toUnmodifiableList()
Collectors.toUnmodifiableSet()
Collectors.toUnmodifiableMap()
```

## GC

G1 的 Full GC 改为并行的标记清除算法, 以减少Full GC 的发生, 从而提高性能和吞吐量

## 其他

`Optional`中新增以下方法:

```Java
// 为 null 时抛出异常
public T orElseThrow()
```