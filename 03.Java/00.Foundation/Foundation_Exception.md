<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [关于](#关于)
- [Error](#error)
- [常见异常](#常见异常)
- [异常处理](#异常处理)
- [方法](#方法)
- [QA](#qa)

<!-- /TOC -->
</details>

## 关于

所有异常的最终父类都是`java.lang.Throwable`,其直接子类有`Error`和`Exception`

前者由运行时逻辑错误导致,包括动态链接失败,jvm错误等,会导致jvm停止,程序不对其做处理.

后者则是可恢复异常,增加异常处理就能够解决.

Java程序运行过程中,如果出现异常就会生成一个异常对象,封装了异常信息后交给Java运行时系统,这是throw过程,

运行时系统接收到异常对象时,会寻找处理该异常的代码并交给它处理,这是catch过程,如果没有这样的处理模块,默认是将异常对象的内部包装信息显示出来.


## Error



## 常见异常

异常主要被分为两类:

* 运行时异常:继承自`java.lang.RuntimeException`,这种异常出现很频繁,对其处理往往会对程序的可读性和性能有很大影响.
* 非运行时异常:直接继承自`java.lang.Exception`,也被称为检查异常,发生于编译时期

![](https://gitee.com/LuVx/img/raw/master/exception.png)

**Exception:**

| 异常                          | 说明         | 发生场景                            |
| ----------------------------- | ------------ | ----------------------------------- |
| NullPointerException          | 空指针异常   | 使用空对象                          |
| IndexOutOfBoundsException     | 索引越界异常 | 数组等下标不在有效范围内            |
| ClassCastException            | 类型转换异常 | 类之间进行转换却不拥有继承/实现关系 |
| ArrayStoreException           | 数组存储异常 | 操作数组时类型不一致                |
| IllegalArgumentException      | 非法参数异常 |                                     |
| NoSuchElementException        |              |                                     |
| UnsupportedOperationException |              |                                     |
| BufferOverflowException       | 缓冲溢出异常 |                                     |
| SQLException                  | SQL异常      | 操作数据库                          |
| IOException                   | IO异常       | 进行IO操作                          |

**Error:**

| 错误                  | 说明               | 发生场景                            |
| --------------------- | ------------------ | ----------------------------------- |
| Error                 | 严重的程序运行问题 | -                                   |
| OutOfMemoryError      | 内存溢出错误       | jvm内存过低或存在的对象非常多       |
| StackOverflowError    | 堆栈溢出错误       | 方法调用链过长                      |
| IllegalAccessError    | 非法访问错误       | 访问Field或Method时违反其可见性声明 |
| AbstractMethodError   | 抽象方法错误       | 调用抽象方法                        |
| ClassCircularityError | 类循环错误         | 初始化类时检测到类之间循环依赖      |



## 异常处理

1. throw:方法内抛出异常,抛出的如果是受检异常,方法声明必须使用throws抛出,否则编译不通过,同时调用者要么处理,要么同样抛出;如果是运行时异常,不用抛出,调用者不用处理或抛出也能编译通过,当然,异常最终都要有处理,多线程由thread.run()抛出,单线程由main()抛出.
2. throws:方法声明中抛出异常,用于表示方法可能会产生异常但不想处理或不想在此处处理.
3. try-catch-finally:具体的处理异常,catch块可以存在多个以针对不同的异常作出不同的处理.

重写方法时,只能和原方法抛出的异常一致或不抛出异常,不能将异常的范围放大或缩小.

finally语句块的代码无论是否出现异常都会执行,

当try中存在return语句时,并不会立即执行return语句,而是进入finally中,

若其中有return等终止程序的代码则会执行,并且不会再返回执行try中的return语句, 如果不存在这样的语句就会返回执行try中的return语句,

因此并不推荐在finally中写入return等语句.

另外,当try中的return的内容有计算,如`return i+=1;return dosome()`,则会先执行语句但不return, 之后执行finally

## 方法

| 方法              | 作用                   |
| ----------------- | ---------------------- |
| getMessage()      | 获取有关异常的信息     |
| printStackTrace() | 输出异常发生时堆栈信息 |

RuntimeException异常和检查型之间的区别就是:

是否强制要求调用者必须处理此异常,
如果强制要求调用者必须进行处理, 那么就使用检查型, 否则就选择非检查型(RuntimeException).
一般来讲, 如果没有特殊的要求, 建议使用RuntimeException异常.


## QA

**`ClassNotFoundException`和`NoClassDefFoundError`的区别**

ClassNotFoundException

类加载时加载阶段找不到需要的class, 如动态加载类或获取类对象时

NoClassDefFoundError

类加载时连接阶段找不到指定的class

类在编译的时候是存在的, 运行的时候却找不到了

查找那些在开发期间存在于类路径下但在运行期间却不在类路径下的类