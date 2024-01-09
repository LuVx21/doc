<details>
<summary>WeakHashMap</summary>
<!-- TOC -->

- [基础](#基础)
- [结构](#结构)

<!-- /TOC -->
</details>


## 基础

**特性**

1. k-v都可以为空
2. k是基于`WeakReference`实现, 配合`ReferenceQueue`使用, 这种类型的键在垃圾回收时会被回收
3. 初始大小, 扩容因子及扩容倍数都和HashMap一样, 分别为 16 和 0.75 和 2
4. 槽位元素超出8后仍然使用拉链方式, 不会转化成红黑树

**weak的实现**

在实现中, 使用了`ReferenceQueue`这个"监听器"来保存被GC回收的"弱键"

在操作WeakHashMap时, 会先同步table, queue, table中保存了全部的键值对, 而queue中保存的是GC回收的键值对;
同步他们, 就是删除table中被GC回收的键值对

即WeakHashMap的实现是通过使用`ReferenceQueue`这个"监听器"来优雅的实现自动删除那些引用不可达的key的

## 结构

```Java
// 
private final ReferenceQueue<Object> queue = new ReferenceQueue<>();
// 存储容器
Entry<K,V>[] table;
// 数组元素类型, key是直接存储在Entry对象中的, value则是定义了新属性
private static class Entry<K,V> extends WeakReference<Object> implements Map.Entry<K,V> {
    V value;
    final int hash;
    Entry<K,V> next;
}
```

增


扩容时会用到以下方法:

```Java
private void expungeStaleEntries() {
    for (Object x; (x = queue.poll()) != null; ) {
        synchronized (queue) {
            Entry<K,V> e = (Entry<K,V>) x;
            int i = indexFor(e.hash, table.length);
            Entry<K,V> prev = table[i];// 队列中的key对应的键值对链的首节点
            Entry<K,V> p = prev;
            while (p != null) {
                Entry<K,V> next = p.next;
                if (p == e) {
                    if (prev == e)
                        table[i] = next;
                    else
                        prev.next = next;
                    e.value = null; // Help GC
                    size--;
                    break;
                }
                prev = p;
                p = next;
            }
        }
    }
}
```

方法主要是同步`table` 和 `queue`, 从table中删除queue中存在的key对应的键值对

具体就是将所有存在于队列中的元素, 会计算出该元素所在的槽位, 然后遍历槽位上的节点链, 将该节点删除

这个方法在`#getTable` 和 `#size` 和 `#resize` 方法中都会调用, 每次调用会同步一次上述两个容器

> 值得注意的是, 弱引用虽然能自动GC, 但table中的entry, 不会随着被删除, 这也是上述方法的作用, 如果没有该方法, 同样会出现OOM

**使用场景**

* 内存缓存

`java.lang.ThreadLocal.ThreadLocalMap` 就使用了 `WeakHashMap` 这样的设计,

其中的`java.lang.ThreadLocal.ThreadLocalMap.Entry`类和`java.util.WeakHashMap.Entry`是一样的设计方法
