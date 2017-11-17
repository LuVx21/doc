---
title: Java类型转换
tags:
- Java
---


# Java类型转换

@(Java)

> [TOC]

http://www.cnblogs.com/hwStar/archive/2011/12/08/2280122.html

在java项目的实际开发和应用中,常常需要用到将对象转为String这一基本功能.本文将对常用的转换方法进行一个总结.
常用的方法有Object.toString(),(String)要转换的对象,String.valueOf(Object)等.下面对这些方法一一进行分析.
## 方法1:采用 Object.toString()方法
请看下面的例子:
```
Object object = getObject();
System.out.println(object.toString());
```

注1
在这种使用方法中,因为java.lang.Object类里已有public方法.toString(),所以对任何严格意义上的java对象都可以调 用此方法.
但在使用时要注意,必须保证object不是null值,否则将抛出NullPointerException异常.采用这种方法时,通常派生类会覆盖Object里的toString()方法.

## 方法2:采用类型转换(String)object方法
这是标准的类型转换,将object转成String类型的值.使用这种方法时,需要注意的是类型必须能转成String类型.
因此最好用instanceof做个类型检查,以判断是否可以转换.否则容易抛出CalssCastException异常.
此外,需特别小心的是因定义为Object 类型的对象在转成String时语法检查并不会报错,这将可能导致潜在的错误存在.这时要格外小心.如:
```
Object obj = new Integer(100);
String strVal = (String)obj;
```
在运行时将会出错,因为将Integer类型强制转换为String类型,无法通过.但是,
```
Integer obj = new Integer(100);
String strVal = (String)obj;
```
如上格式代码,将会报语法错误.
此外,因null值可以强制转换为任何java类类型,(String)null也是合法的.

## 方法3:采用String.valueOf(Object)
String.valueOf(Object)的基础是Object.toString().但它与Objec.toString()又有所不同.
在前面方法1的分析中提到,使用后者时需保证不为null.但采用第三种方法时,将不用担心object是否为null值这一问题.
为了便于说明问题,我们来分析一下相关的源代码.Jdk里String.valueOf(Object)源码如下:
```
public static String valueOf(Object obj) {
       return (obj == null) ? "null" : obj.toString();
}
```
从上面的源码可以很清晰的看出null值不用担心的理由.但是,这也恰恰给了我们隐患.
我们应当注意到,当object为null时,String.valueOf(object)的值是字符串"null",而不是null！！！
在使用过程中切记要注意.
试想一下,如果我们用 
```
if(String.valueOf(object)== null){
	System.out.println(“传入的值是null！”);
}
```
这样的语句将可能会发生什么问题.
再想一下,向控制台输出时,在视觉上如下语句在执行的结果上有什么不同:
```
System.out.println(String.valueOf((Object)null)); 注2
System.out.println(null);
```
我们看到的输出将是一模一样的东西:null,但它们意义相同吗？
注1:光就System.out.println的使用来说,语句System.out.println(object.toString()); 换为System.out.println(object); 更好.
此处如是用法主要是为了说明Object#toString()的用法.
注2:推荐使用System.out.println(String.valueOf((Object)null)); 
不推荐使用System.out.println(String.valueOf(null));

在使用重载方法时,这样做是一个好的习惯.
以上是对object对象转换为String的一些总结.
new Integer.valueof()返回的是Integer的对象.
Integer.parseInt() 返回的是一个int的值.

new Integer.valueof().intValue();返回的也是一个int的值.
Integer.valueof(String s)是将一个包装类是将一个实际值为数字的变量先转成string型再将它转成Integer型的包装类对象(相当于转成了int的对象)这样转完的对象就具有方法和属性了.
而Integer.parseInt(String s)只是将是数字的字符串转成数字,注意他返回的是int型变量不具备方法和属性.

1 字符串转换成数据
字符串转换成整数:
```
String MyNumber ="1234";
int MyInt = Integer.parseInt(MyNumber);
```
字符串转换成byte, short, int, float, double, long等数据类型,可以分别参考Byte, Short, Integer, Float, Double, Long类的parseXXX方法.

2 数据转换成字符串
整数转换成字符串:
```
int MyInt = 1234;
String MyString = "" + MyInt;
```
其它数据类型可以利用同样的方法转换成字符串.

3 十进制到其他进制的转换
十进制整数转换成二进制整数,返回结果是一个字符串:
Integer.toBinaryString(int i);
Integer和Long提供了toBinaryString, toHexString和toOctalString方法,可以方便的将数据转换成二进制、十六进制和八进制字符串.功能更加强大的是其toString(int/long i, int radix)方法,可以将一个十进制数转换成任意进制的字符串形式.
byte, short, float和double等数据类型,可以利用Integer或者是Long的toBinaryString, toHexString, to OctalString和toString方法转换成其他进制的字符串形式.

4 其它进制到十进制的转换
五进制字符串14414转换成十进制整数,结果是1234:

System.out.println(Integer.valueOf("14414", 5);

Integer和Long提供的valueOf(String source, int radix)方法,可以将任意进制的字符串转换成十进制数据.

5 整数到字节数组的转换
```
public static byte[] toByteArray(int number){
   int temp = number;
   byte[] b=new byte[4];
   for (int i = b.length - 1; i > -1; i--)
   {
      b[i] = new Integer(temp & 0xff).byteValue();
      temp = temp >> 8;
   }
   return b;
}
```
6 字节数组到整数的转换

```
public static int toInteger(byte[] b){
   int s = 0;
   for (int i = 0; i < 3; i++)
   {
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

7 短整数与字节数组之间的相互转换
short与int之间的区别在于short是两个字节的,而int是四个字节的.因此,只需要将5 与6 中的范例程序小做改动,即可实现短整数与字节数组之间的相互转换.

8 字节数组转换成双精度浮点数

```
public double toDouble(byte[] b){
   long l = 0;
   Double D = new Double(0.0);
   l = b[0];
   l |= ((long)b[1]<<8);
   l |= ((long)b[2]<<16);
   l |= ((long)b[3]<<24);
   l |= ((long)b[4]<<32);
   l |= ((long)b[5]<<40);
   l |= ((long)b[6]<<48);
   l |= ((long)b[7]<<56);
   return D.longBitsToDouble(l);
}
```

9 布尔类型转换成字符串
第一种方法是:

boolean bool = true;
String s = new Boolean(bool).toString();//将bool利用对象封装器转化为对象
s.equals("true");

第二种方法是:

boolean bool = true;
String s = String.valueOf( bool );
首先,从代码长度上讲第二种方法明显要比第一种方法简洁；其次,第一种方法在转化过程中多引入了一个完全没有必要的对象,因此,相对第二种方法来说这就造成了内存空间的浪费,大大减慢了运行速度.所以,推荐使用第二种方法.

10 数字类型与数字类对象之间的转换
```
byte b = 169;
Byte bo = new Byte( b );
b = bo.byteValue();
short t = 169;
Short to = new Short( t );
t = to.shortValue();
int i = 169;
Integer io = new Integer( i );
i = io.intValue();
long l = 169;
Long lo = new Long( l );
l = lo.longValue();
float f = 169f;
Float fo = new Float( f );
f = fo.floatValue();
double d = 169f;
Double dObj = new Double( d );
d = dObj.doubleValue();
```
5.0 String 转 Integer
```
Integer in=new Integer(String s);
Integer in=new Integer(Integer.parseInt(String s));
```

5.1 String 转 int
```
int i = Integer.parseInt([String]); 或 i = Integer.parseInt([String],[int radix]);
int i = Integer.valueOf(my_str).intValue();
```

5.2 如何将整数 int 转换成字串 String ?

A. 有3种方法:
```
String s = String.valueOf(i);
String s = Integer.toString(i);
String s = "" + i;
```
注: Double, Float, Long 转成字串的方法大同小异.

5.3 String 转 Date
 
```
java.util.Date date=null;
date=java.sql.Date.valueOf(String s);
```