<details>
<summary>点击展开目录</summary>
<!-- TOC -->


<!-- /TOC -->
</details>

ConcurrentSkipListSet

基于 `ConcurrentSkipListMap` 实现

```Java
private final ConcurrentNavigableMap<E,Object> m;
public ConcurrentSkipListSet() {
    m = new ConcurrentSkipListMap<E,Object>();
}
public boolean add(E e) {
    return m.putIfAbsent(e, Boolean.TRUE) == null;
}
public boolean remove(Object o) {
    return m.remove(o, Boolean.TRUE);
}
```
