---
title: Java8:Lambda
date:
tags:
- Lambda
---
<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [函数式接口](#函数式接口)
    - [消费型](#消费型)
    - [供给型](#供给型)
    - [函数型](#函数型)
    - [断言型](#断言型)
    - [其他](#其他)
    - [自定义](#自定义)
- [Lambda](#lambda)
    - [基础语法](#基础语法)
    - [变量访问](#变量访问)
    - [静态引用](#静态引用)
- [jdk自带的函数式接口](#jdk自带的函数式接口)
- [jdk中的lambda](#jdk中的lambda)

<!-- /TOC -->
</details>

## 函数式接口

4大核心函数式接口

| 接口             | 方法                | 说明       |
| :--------------- | :------------------ | :--------- |
| `java.util.function.Consumer<T>`    | `void accept(T t)`  | 消费型接口 |
| `java.util.function.Supplier<T>`    | `T get()`           | 供给型接口 |
| `java.util.function.Function<T, R>` | `R apply(T t)`      | 函数型接口 |
| `java.util.function.Predicate<T>`   | `boolean test(T t)` | 断言型接口 |

### 消费型

| 接口                   | 方法                             |
| :--------------------- | :------------------------------- |
| `BiConsumer<T, U>`     | `void accept(T t, U u)`          |
| `IntConsumer`          | `void accept(int value)`         |
| `LongConsumer`         | `void accept(long value)`        |
| `DoubleConsumer`       | `void accept(double value)`      |
| `ObjIntConsumer<T>`    | `void accept(T t, int value)`    |
| `ObjLongConsumer<T>`   | `void accept(T t, long value)`   |
| `ObjDoubleConsumer<T>` | `void accept(T t, double value)` |

### 供给型

| 接口              | 方法                     |
| :---------------- | :----------------------- |
| `BooleanSupplier` | `boolean getAsBoolean()` |
| `IntSupplier`     | `int getAsInt()`         |
| `LongSupplier`    | `long getAsLong()`       |
| `DoubleSupplier`  | `double getAsDouble()`   |

### 函数型

| 接口                         | 方法                                 |
| :--------------------------- | :----------------------------------- |
| `BiFunction<T, U, R>`        | `R apply(T t, U u)`                  |
| `ToIntBiFunction<T, U>`      | `int applyAsInt(T t, U u)`           |
| `ToLongBiFunction<T, U>`     | `long applyAsLong(T t, U u)`         |
| `ToDoubleBiFunction<T, U>`   | `double applyAsDouble(T t, U u)`     |
| ---------------------------- | ------------------------------------ |
| `IntFunction<R>`             | `R apply(int value)`                 |
| `LongFunction<R>`            | `R apply(long value)`                |
| `DoubleFunction<R>`          | `R apply(double value)`              |
| ---------------------------- | ------------------------------------ |
| `ToIntFunction<T>`           | `int applyAsInt(T value)`            |
| `LongToIntFunction`          | `int applyAsInt(long value)`         |
| `DoubleToIntFunction`        | `int applyAsInt(double value)`       |
| ---------------------------- | ------------------------------------ |
| `ToLongFunction<T>`          | `long applyAsLong(T value)`          |
| `IntToLongFunction`          | `long applyAsLong(int value)`        |
| `DoubleToLongFunction`       | `long applyAsLong(double value)`     |
| ---------------------------- | ------------------------------------ |
| `ToDoubleFunction<T>`        | `double applyAsDouble(T value)`      |
| `IntToDoubleFunction`        | `double applyAsDouble(int value)`    |
| `LongToDoubleFunction`       | `double applyAsDouble(long value)`   |


### 断言型

| 接口                | 方法                         |
| :------------------ | :--------------------------- |
| `BiPredicate<T, U>` | `boolean test(T t, U u)`     |
| `IntPredicate`      | `boolean test(int value)`    |
| `LongPredicate`     | `boolean test(long value)`   |
| `DoublePredicate`   | `boolean test(double value)` |

### 其他

| 接口                   | 方法                                              |
| :--------------------- | :------------------------------------------------ |
| `BinaryOperator<T>`    | 继承自`BiFunction<T,T,T>`                         |
| `IntBinaryOperator`    | `int applyAsInt(int left, int right)`             |
| `LongBinaryOperator`   | `long applyAsLong(long left, long right)`         |
| `DoubleBinaryOperator` | `double applyAsDouble(double left, double right)` |
| `UnaryOperator<T>`     | 继承自`Function<T, T>`                            |
| `IntUnaryOperator`     | `int applyAsInt(int operand)`                     |
| `LongUnaryOperator`    | `long applyAsLong(long operand)`                  |
| `DoubleUnaryOperator`  | `double applyAsDouble(double operand)`            |
| `Collector<T, A, R>`   | 收集器,汇聚操作, 流式运算collect方法接受此类型参数|

> 工具类Collectors

### 自定义

有且仅有一个抽象方法的接口, 通常使用`@FunctionalInterface`修饰, 不修饰也没关系,该注解仅起到标识的作用

保证接口中有且仅有一个抽象方法, 可以有任意个默认方法(default)

```Java
@FunctionalInterface
public interface Formula {
    int plusOne(int a);

    default int plusSelf(int a) {
        return a + a;
    }
}
```

Usage:
```Java
// java8前可以实现接口或局部内部类使用, 现在可以使用lambda
Formula formula = (a) -> a + 1;
System.out.println(formula.plusOne(100) + " " + formula.plusSelf(100));
```
> 默认方法不能被重写

## Lambda

### 基础语法

![](https://gitee.com/LuVx/img/raw/master/lambda.jpg)
> 单个参数`()`也可省略

### 变量访问

```Java
// Lambda能访问局部变量, 成员变量, 但要求变量final或等效于final
// Variable used in lambda expression should be final or effectively final
@Test
public void testScopes() {
    String b = "a";
    // b = "b";// 放开编译出错
    Function<String, String> function = from -> from.concat(b);
}
```

### 静态引用

使用一个类的`类名::method`(静态方法)或`对象名::method`(普通方法)或``类名::new``(构造函数)作为函数式接口的抽象方法的方法体

使用::获取方法或者构造函数的引用,既可以是类的也可以是对象的

Usage:
```Java
@FunctionalInterface
public interface Convertable<F, T> {
    T convert(F from);
}
@Getter
@ToString
@NoArgsConstructor
@AllArgsConstructor
public class Refrenced {
    private String name;

    public static String toLowerCase(String s) {
        return s.toLowerCase();
    }

    public String startsWith(String s) {
        return String.valueOf(s.charAt(0));
    }
}
```

Usage:
```Java
Convertable<String, String> converter = Refrenced::toLowerCase;
String converted = converter.convert("Java");

Refrenced something = new Refrenced();
converter = something::toUpperCase;
converted = converter.convert("Java");

Convertable<String, Refrenced> converter2 = Refrenced::new;
Refrenced ref = converter2.convert("Java");

Convertable<Refrenced, String> converter1 = Refrenced::captureName;
converted = converter1.convert(something);
```
> 泛型的第一个参数为入参类型, 第二个为返回值类型
> 如果想要使用`类名::非静态方法`, 则方法不能有参数, 泛型也要修改为类

## jdk自带的函数式接口

位于包`java.util.function`下
```Java
Supplier<User> personSupplier = User::new;
User user = personSupplier.get();
Consumer<String> consumer = r -> System.out.println("Hello, " + r);
consumer.accept("World");
```

**条件语句**

```Java
Predicate<String> predicate = s -> s.length() > 0;
System.out.println("长度>0: " + predicate.test("foo"));
System.out.println("长度<=0: " + predicate.negate().test("foo"));
```
> 配合Optional做参数校验

## jdk中的lambda

**比较器**

```Java
// 自定义
// Comparator<String> comparator = new Comparator<String>() {
//         @Override
//         public int compare(String a, String b) {
//             return a.compareTo(b);
//         }
//     };
// comparator = (String a, String b) -> {
//     return a.compareTo(b);
// }
// comparator = (String a, String b) -> a.compareTo(b);
comparator = (a, b) -> a.compareTo(b);
comparator = Comparator.comparing(String::toString);
/// 逆序有reverseOrder()方法
comparator = Comparator.naturalOrder();
```

**集合操作**
```Java
list.forEach(user -> System.out.println(user));
list.forEach(user -> user.getUserName());
list.forEach(User::getUserName);
map.forEach((k,v) -> System.out.println("key: " + k + " value: " + v));
list.sort(Comparator.comparing(User::getUserName, String.CASE_INSENSITIVE_ORDER));
Collections.sort(list, comparator);
```

**线程创建**
```Java
// jdk8前
Runnable r = new Runnable() {
    @Override
    public void run() {
        method();
    }
}
// jdk8后
Runnable r = () -> method();
```

**文件读取**
```Java
File dir = new File("/home/dir/");
// before
FileFilter directoryFilter = new FileFilter() {
    public boolean accept(File file) {
        return file.isDirectory();
    }
};
// 8
File[] dirs = dir.listFiles(f -> f.isDirectory());
```

