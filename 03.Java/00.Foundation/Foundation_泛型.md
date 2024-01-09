---
title: 泛型
date:
tags:
- Java
---

<!-- TOC -->

- [关于](#关于)
- [通配符](#通配符)
- [泛型方法](#泛型方法)

<!-- /TOC -->

## 关于

* 将异常提前至编译期
* 使用泛型的集合只能存放同一种数据类型(编译器进行泛型检查)
* 使用场景:泛型类, 泛型方法, 泛型集合
* 数组不支持泛型

> 泛型类一般使用字母 T 作为泛型标志
> 泛型集合通常使用T(Type),K(Key),V(Value),E(Element)等作为标志,如Map<K, V>
> `<>`中类型存在父子关系,但泛型List<Number>和List<Integer>不具有

## 通配符

类型通配符一般是使用`?`代替方法具体的类型实参,即`<>`中是什么类型方法都可以处理.
类型通配符上限和类型通配符下限,如`<? extends Number>`和`<? super Number>`

使用了`<?>`的集合,使用add,get以及集合拥有的其他方法,因为此时不能确定类型,
使用了上限通配符的集合等,不能向集合中添加元素,即使用`add()`等方法(`add(null)`除外)
下限则无此限制,但不能从其中取数据,即使用`get()`方法

泛型中使用`instanceof`判断类型,只能使用`<?>`,如
```Java
public static void check(List<?> list) {
    if (list instanceof ArrayList<?>) {
    }
}
```

## 泛型方法

```Java
public class R<T> {
    public R<T> of() {
        return new R<>();
    }

    public static <T> R<T> of1() {
        return new R<>();
    }
}
```

**泛型擦除**

泛型只存在编译阶段,运行阶段不存在泛型(此时类型已经确定)

**泛型中extends和super的**

