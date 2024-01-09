<details>
<summary>点击展开目录</summary>
<!-- TOC -->

    - [主要](#主要)
    - [并发](#并发)
    - [接口](#接口)
- [Collectors](#collectors)
- [Optional](#optional)

<!-- /TOC -->
</details>

## 主要

* lamda表达式
* stream及集合的 stream 操作
* 提升HashMap的性能: hash 碰撞时使用红黑树代替了原来的链表
* 新的日期时间接口

## 并发

`java.util.concurrent.locks.StampedLock`

## 接口

可以使用 `default` 声明非静态的默认方法(带有方法体)

声明 `static` 方法不能搭配 `default` 使用, 同时需要实现方法

# Collectors


groupingBy


partitioningBy

# Optional

Optional.of()
Optional.ofNullable()

Optional(T value),empty(),of(T value),ofNullable(T value)


```Java
//Java8前
if (obj != null) {
    ...;
}

// Java8
Optional.ofNullable(obj).ifPresent(u -> {
    ...;
});

Optional.ofNullable(user)
    .filter(u -> "zhangsan".equals(u.getIntention()))
    .orElseGet(() -> {
        CallLogDto user1 = new CallLogDto();
        user1.setIntention("zhangsan");
        return user1;
    });
```
orElseThrow
orElseGet

> ifPresent???


流式迭代中获取到索引/下标:

```Java
List<Sttring> list = new ArrayList<>();
list.add("1");
list.add("2");
list.add("3");
list.add("4");
list.add("5");

Stream.iterate(0, i -> i + 1)
.limit(list.size())
.forEach(i ->
        {
            Sytem.out.println(list.get(i));
        }
 );

IntStream.range(0, lists.size())
         .mapToObj(i -> handle(lists.get(i), i))
         .collect(Collectors.toList());
```

https://www.cjavapy.com/article/260/

http://www.importnew.com/10360.html



[Java8的新特性](https://segmentfault.com/a/1190000004419611)