<details>
<summary>LRU算法</summary>
<!-- TOC -->

- [方式1](#方式1)
- [方式2](#方式2)
- [方式3](#方式3)
- [并发](#并发)

<!-- /TOC -->
</details>

设计和构建一个"最近最少使用"缓存, 该缓存会删除最近最少使用的项目.

缓存应该从键映射到值(允许你插入和检索特定键对应的值), 并在初始化时指定最大容量.

当缓存被填满时, 它应该删除最近最少使用的项目.


运用你所掌握的数据结构, 设计和实现一个 LRU (最近最少使用) 缓存机制.

获取数据 get(key) - 如果关键字 (key) 存在于缓存中, 则获取关键字的值(总是正数), 否则返回 -1.

写入数据 put(key, value) - 如果关键字已经存在, 则变更其数据值;如果关键字不存在, 则插入该组「关键字/值」.当缓存容量达到上限时, 它应该在写入新数据之前删除最久未使用的数据值, 从而为新的数据值留出空间.

进阶:

你是否可以在 `O(1)` 时间复杂度内完成这两种操作?

[LeetCode-146](https://leetcode-cn.com/problems/lru-cache/)

## 方式1

```Java
public class LRUCache extends AbstractMap {
    @Override
    public Object put(Object key, Object value) {
        return null;
    }

    @Override
    public Set<Entry> entrySet() {
        // super.entrySet();
        return super.keySet();
        // return null;
    }

    @Data
    private static class Node {
        Object value;
        Node   next;
    }
}
```

## 方式2


## 方式3

```Java
public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private       int size = 0;
    private final int MAX_SIZE;

    public LRUCache(int size) {
        super(size, 0.75f, true);
        MAX_SIZE = size;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > MAX_SIZE;
    }
}
```
`LinkedHashMap(int initialCapacity, float loadFactor, boolean accessOrder)` 设置`accessOrder`为true, 则会按照访问顺序读取

当LinkedHashMap调用 `put` 或者 `putAll` 成功插入键值时, 会调用`removeEldestEntry`方法, 根据该方法的返回值决定是否删除最老对象,

因此为了保证map的size不超过某个值, 可以重写`removeEldestEntry`方法, 返回true, 删除最老对象.

## 并发

设计并实现一个简易的缓存框架, 要求⽀持并发的读写和LRU缓存淘汰算法, 并考虑性能
