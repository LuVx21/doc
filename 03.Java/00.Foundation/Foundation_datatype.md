---
title: Java基础:数据类型
tags:
- Java
---
<!-- TOC -->

- [基础知识](#基础知识)
- [基本类型](#基本类型)
- [to String](#to-string)
    - [方法1:Object.toString()](#方法1objecttostring)
    - [方法2:(String)object](#方法2stringobject)
    - [方法3:String.valueOf(object)](#方法3stringvalueofobject)
- [to Num](#to-num)
- [to Array](#to-array)
- [to Date](#to-date)
- [进制](#进制)

<!-- /TOC -->
# 基础知识

1字节(byte)=8位(bit)

# 基本类型

8种=4整型+2浮点型+1字符型+1布尔型

| 类型    | 字节(位数) | 默认  | 说明                                          |
| :------ | :--------- | :---- | :-------------------------------------------- |
| byte    | 1byte(8)   | 0     |                                               |
| short   | 2byte(16)  | 0     |                                               |
| int     | 4byte(32)  | 0     |                                               |
| long    | 8byte(64)  | 0L    |                                               |
| float   | 4byte(32)  | 0.0f  | 单精度,8位有效数字,数量级38                   |
| double  | 8byte(64)  | 0.0d  | 双精度,17位有效数字,数量级308                 |
| char    | 2byte(16)  |       | 单一的Unicode字符,范围:\u0000~\uffff(0~65535),能存储一个汉字 |
| boolean | 1位        | false |                                               |

> BigInteger 支持任意精度的整数
> BigDecimal 支持任何精度的浮点数

**long**

float: 32位(1符号位+8指数位+23尾数位)
指数范围: -128~127 → 表示范围: -2^128~2^127

double: 64位(1符号位+11指数位+52尾数位)
指数范围: -1024~1023 → 表示范围: -2^1024~2^1023

> 因规格化表示, 小数点左边一位一定为1, 所以实际尾数为比上述多1
参考阅读[浮点数存储结构](https://blog.csdn.net/ideality_hunter/article/details/78432486)

**和包装类的区分**


**强/弱类型**

```shell
# 弱类型:代表性语言为JavaScript
> "1"+2
'12'

# 弱类型:代表性语言为Python
>>> "1"+2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: cannot concatenate 'str' and 'int' objects
```

**动态/静态类型**

```shell
# 动态类型:代表性语言为Python,JavaScript
>>> a = 1
>>> type(a)
<type 'int'>
>>> a = "a"
>>> type(a)
<type 'str'>

# 静态类型:代表性语言为Java
int a = 1;
a = "a";
Error:(7, 13) java: 不兼容的类型: java.lang.String无法转换为int
```
> 以上强/弱/动态/静态类型的示例仅为参考, 用于理解术语的概念

# to String

## 方法1:Object.toString()
请看下面的例子:
```Java
Object object = getObject();
System.out.println(object.toString());
```

在这种使用方法中, 因为java.lang.Object类里已有public方法toString(), 所以对任何严格意义上的java对象都可以调用此方法.

但在使用时要注意, 必须保证object不是null值, 否则将抛出NullPointerException异常.

采用这种方法时, 通常派生类会覆盖Object里的toString()方法.

## 方法2:(String)object

这是标准的类型转换, 将object转成String类型的值.

使用这种方法时, 需要注意的是类型必须能转成String类型,因此最好用instanceof做个类型检查, 以判断是否可以转换.否则容易抛出CalssCastException异常.

此外, 应特别小心的是因定义为Object 类型的对象在转成String时语法检查并不会报错, 这将可能导致潜在的错误.这时要格外小心.如:

```Java
Object obj = new Integer(100);
String strVal = (String)obj;
```
在运行时将会出错, 因为将Integer类型强制转换为String类型, 无法通过.但是,
```Java
Integer obj = new Integer(100);
String strVal = (String)obj;
```
如上格式代码, 将会报语法错误.
此外, 因null值可以强制转换为任何java类类型, (String)null也是合法的.

## 方法3:String.valueOf(object)

String.valueOf(object)的基础是Object.toString().但它与Objec.toString()又有所不同.

在前面方法1的分析中提到, 使用后者时需保证不为null.但采用第三种方法时, 将不用担心object是否为null值这一问题.

为了便于说明问题, 我们来分析一下相关的源代码.Jdk里String.valueOf(Object)源码如下:

```Java
public static String valueOf(Object obj) {
       return (obj == null) ? "null" : obj.toString();
}
```
从上面的源码可以很清晰的看出null值不用担心的理由.

但是, 这也恰恰给了我们隐患.我们应当注意到, 当object为null时, String.valueOf(object)的值是字符串"null", 而不是null！！！
在使用过程中切记要注意.
试想一下, 如果我们用

```Java
if(String.valueOf(object)== null){
	System.out.println("传入的值是null");
}
```
这样的语句将可能会发生什么问题.
再想一下, 向控制台输出时, 在视觉上如下语句在执行的结果上有什么不同:
```Java
System.out.println(String.valueOf((Object)null));
System.out.println(null);
```
推荐使用`String.valueOf((Object)null); `,不推荐使用`String.valueOf(null);`



**整数转换成字符串**

```java
int num = 1234;
String str = "" + num;
String str = String.valueOf(num);
String str = Integer.toString(num);
```

>  Double, Float, Long 转成字串的方法大同小异.

**布尔类型转换成字符串**

```java
boolean bool = true;
// String s = new Boolean(bool).toString();
String s = String.valueOf(bool);
```




# to Num

**字符串转换成数据**

```Java
int num = Integer.parseInt("1234");
int num = Integer.valueOf("1234").intValue();
```

**字节数组到整数的转换**
```Java
public static int toInteger(byte[] b){
    int s = 0;
    for (int i = 0; i < 3; i++) {
        if (b[i] > 0)
            s = s + b[i];
    } else {
        s = s + 256 + b[i];
        s = s * 256;
    }
    if (b[3] > 0)
        s = s + b[3];
    else
        s = s + 256 + b[3];
    return s;
}
```

# to Array

**整数到字节数组**
```Java
public static byte[] toByteArray(int number){
   int temp = number;
   byte[] b = new byte[4];
   for (int i = b.length - 1; i > -1; i--) {
      b[i] = new Integer(temp & 0xff).byteValue();
      temp = temp >> 8;
   }
   return b;
}
```

# to Date

```Java
java.util.Date date = null;
date = java.sql.Date.valueOf(String s);
```

# 进制

**十进制整数转换成二进制整数**

Long/Integer类提供一下方法进行进制转换,返回结果是一个字符串:
```Java
// 2进制
Integer.toBinaryString(int/long i)
// 8进制
Integer.toOctalString(int/long i)
// 16进制
Integer.toHexString(int/long i)
// radix进制
Integer.toString(int/long i, int radix)
```

**其它进制到十进制**

```Java
Integer.valueOf("14414", 5);
```

Integer/Long提供的valueOf()方法, 可以将任意进制的字符串转换成十进制数据.



