<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [jshell](#jshell)
- [接口](#接口)
- [try](#try)
- [`<>`](#)
- [String](#string)
- [集合](#集合)
- [Stream](#stream)
- [其他](#其他)

<!-- /TOC -->
</details>

## jshell

命令行中直接写Java代码

## 接口

接口中可以定义私有方法, 仅供在内部调用

## try

可自动关闭的资源可以在`try()`外定义

```java
BufferedReader br = new BufferedReader(inputString);
try (br) {
    return br.readLine();
}
```

## `<>`

匿名内部类的形式, 右侧的`<>`内需要声明类型, 而在9则不需要:
```Java
Set<String> set0 = new HashSet<>() {
};
```

## String

String 类的底层数据结构类型从 `char` 变成 `byte`
```Java
private final char value[];
// ↓
private final byte[] value;
```

`AbstractStringBuilder` 发生同样的更改

## 集合

针对 List, Set, Map 在接口层面上新增了静态`of()`方法, 可以方便地创建不可变集合

```Java
Set<String> set = Collections.unmodifiableSet(new HashSet<String>() {
    {
        add("a");
        add("b");
        add("c");
    }
});
Set<String> set = Set.of("a", "b", "c");
```

对于Map:

接口除了新增了 `of()`, 还新增了`ofEntries()`, `entry()`方法能快速创建 Map 对象

创建结果为继承自`ImmutableCollections.AbstractImmutableMap`类的`MapN`类的对象, 可以看出这样创建的对象都是不可变的

> 类似的还有 `ImmutableCollections.AbstractImmutableList`和`ImmutableCollections.AbstractImmutableSet`类

## Stream

`java.util.stream.Stream`接口中增加了4个方法:

```Java
// 在有序的Stream中, 返回从开头开始的尽量多的元素
default Stream<T> takeWhile(Predicate<? super T> predicate)
default Stream<T> dropWhile(Predicate<? super T> predicate)
public static<T> Stream<T> ofNullable(T t)
public static<T> Stream<T> iterate(T seed, Predicate<? super T> hasNext, UnaryOperator<T> next)
```

`Collectors`类新增 2 个方法:

```Java
public static <T, A, R> Collector<T, ?, R>
filtering(Predicate<? super T> predicate, Collector<? super T, A, R> downstream)
public static <T, U, A, R> Collector<T, ?, R>
flatMapping(Function<? super T, ? extends Stream<? extends U>> mapper, Collector<? super U, A, R> downstream)
```

## 其他

`Optional`类新增以下方法:

```Java
public void ifPresentOrElse(Consumer<? super T> action, Runnable emptyAction)
public Optional<T> or(Supplier<? extends Optional<? extends T>> supplier)
public Stream<T> stream()
```

