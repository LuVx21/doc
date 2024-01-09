<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [跨行字符串](#跨行字符串)
- [instanceof](#instanceof)
- [switch](#switch)
- [Record Type](#record-type)

<!-- /TOC -->
</details>

记录语法层面的特性, 其他可阅读[JDK 14 Release Notes](http://jdk.java.net/14/release-notes)

## 跨行字符串

```Java
String sql = """
        select * from emp
        where id = ?
        """;
```


## instanceof

```Java
Object o = "abc";
if (o instanceof String s) {
    System.out.println(s);
}
```

## switch

```Java
int i = 1;
switch (i) {
    case 1, 3, 5, 7 -> System.out.println("奇数:" + i);
    default -> System.out.println("偶数:" + i);
}
```

```Java
String s = switch (i) {
    case 1, 3, 5, 7:
        yield "奇数:" + i;
    default:
        yield "偶数:" + i;
};
```
> 13时候作为预览特性加入, 14 成为正式语法

## Record Type

类似Enum 类, 也是一种约束型的类定义方式

可用于创建只读对象

如定义:
```Java
public record User(Long id, String name, Integer age) {
}
```

其内部其实如下:
```Java
public final class User extends java.lang.Record {
    private final java.lang.Long id;
    private final java.lang.String name;
    private final java.lang.Integer age;

    public User(java.lang.Long id, java.lang.String name, java.lang.Integer age) { /* compiled code */ }

    public java.lang.String toString() { /* compiled code */ }

    public final int hashCode() { /* compiled code */ }

    public final boolean equals(java.lang.Object o) { /* compiled code */ }

    public java.lang.Long id() { /* compiled code */ }

    public java.lang.String name() { /* compiled code */ }

    public java.lang.Integer age() { /* compiled code */ }
}
```

可以看到属性全部是 `final` 的, 对外也只提供了查询方法
