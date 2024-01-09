<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [方法](#方法)

<!-- /TOC -->
</details>


我们在使用BigDecimal时, 使用它的BigDecimal(String)构造函数创建对象才有意义. 其他的如BigDecimal b = new BigDecimal(1)这种, 还是会发生精度丢失的问题.

一般使用BigDecimal来解决商业运算上丢失精度的问题的时候, 声明BigDecimal对象的时候一定要使用它构造参数为String的类型的构造函数


## 方法


add(BigDecimal)
subtract(BigDecimal)
multiply(BigDecimal)
divide(BigDecimal)