---
title: 位运算
tags:
- Java
---
<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [位运算(bitwise operation)](#位运算bitwise-operation)
    - [Q](#q)
    - [进制的API](#进制的api)
    - [1.移位运算符:](#1移位运算符)
    - [2.逻辑运算符:](#2逻辑运算符)
    - [3.优先级:](#3优先级)
- [性质](#性质)
    - [交换两个变量的值](#交换两个变量的值)
    - [进制的转换](#进制的转换)
    - [取余](#取余)
- [应用](#应用)

<!-- /TOC -->
</details>


# 位运算(bitwise operation)
## Q

```java
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

## 进制的API

```java
Integer.toBinaryString(Integer.MAX_VALUE)
```

## 1.移位运算符:

|运算符|说明|
|:---|:---|
|`<<`|左移, 低位补0, *2^n|
|`>>`|有符号右移 /2^n, 若值为正, 则在高位插入0; 若值为负, 则在高位插入1|
|`>>>`|无符号右移, 无论正负, 都在高位插入0|

* 左移位数多于该类型最大位数时, 先对移动位数取余(%), 再左移(如int:40%32=8, 移40位, 实质是8位)
* 正数时, `>>>`等于`>>`, 负数时变为正数


> 左移位数过多时, 可能成为负数
> byte, short移位前会转换为int(32位)再移位运算
> double, float不能移位运算


## 2.逻辑运算符:

|运算符|说明|
|:---|:---|
|`&`|位与, 全为1则值为1|
|`|`|位或, 有1则为1|
|`^`|位异或 相同为0, 不同为1?|
|`~`|位非 取反|

> 0^0=0, 0^1=1 0异或任何数＝任何数
> 1^0=1, 1^1=0 1异或任何数－任何数取反
> 任何数异或自己＝把自己置0
> 数a两次异或同一个数b(a=a^b^b)仍然为原值a.


## 3.优先级:
`~`的优先级最高, 其次是`<<`, `>>`和`>>>`, 再次是`&`, 然后是`^`, 优先级最低的是`|`.

>  注意:
1. `>>>`和`>>`的区别是:在执行运算时, `>>>`运算符的操作数高位补0, 而`>>`运算符的操作数高位移入原来高位的值.
2. 右移一位相当于除以2, 左移一位(在不溢出的情况下)相当于乘以2; 移位运算速度高于乘除运算.
3. 若进行位逻辑运算的两个操作数的数据长度不相同, 则返回值应该是数据长度较长的数据类型.
4. 按位异或运算可以不使用临时变量完成两个值的交换, 也可以使某个整型数的特定位的值翻转.
5. 按位与运算可以用来屏蔽特定的位, 也可以用来取某个数型数中某些特定的位.
6. 按位或运算可以用来对某个整型数的特定位的值置l.

# 性质

* 一个数异或同一个数两次还是原数
* 一个数异或1得到它的前一个或后一个数(偶数得到后一个,奇数得到前一个)

## 交换两个变量的值

交换a b两个变量的值, 几乎是每一个人在学编程的时候都要接触的.现在我们想这个问题, 我有两个瓶子, A瓶装着果汁, B瓶装着醋, 要是A瓶装醋, B瓶装果汁, 如何做呢? 通常, 我们会用下面这段代码:
```
int temp;

temp = a;
a = b;
b = temp;
```
这就像我们找来一个新瓶子C, 先将果汁倒入新瓶子, 然后醋倒入A, 果汁再倒入B.这是很容易想到的办法.那么如果没有别的瓶子了呢? 看看这个:
```
a = a + b; //此时a包含a 和 b
b = a - b;
a = a - b;
```
只有两个瓶子, 那么我们只好把它们倒在一起, 然后将混合物中的果汁倒入B.不可思议是吗? 别急, 我们将果汁和醋倒在一起的时候, B瓶还在, 这可是程序呀, B瓶仍然装着醋, 这时我们就可以将(混合－醋)的值给B, 也就是果汁, 再将(混合－果汁)的值给A, 完成！但是, 这种方式有一个弊端:有可能a＋b的值会超出int型的范围.

基于同样的想法, 我们还可以用一下的代码:
```
a = a ^ b;
b = a ^ b;  //实际上是(a^b)^b 也就是a异或了b两次, 等号右边是a的值
a = a ^ b;  //此时b里面已经是"果汁", 实际上是(a^b)^a, 也就是b异或了a两次, 是b
```
第一步之后, 原来a占用了多少位依旧是多少位, 绝对不会发生数据的溢出.

## 进制的转换

10进制转16进制为例

接触位运算之前, 进制转换我是这么操作的:

```java
class turn10_16{
    public static void main(String[] args){
    int n=200; //n就是代转换的数字
    boolean out_turn=false; //输出时用, 去掉输出时候高位上的‘0’
    int[] s=new int[20]; //将转换后的十六进制数存放在s［］数组中

    while(n>0){
            int i=0;
            s[i]++;
            while(s[i]>15){ //逢16进一, 并且检查下一位
                            //是不是16, 如果是, 再进一
                s[i]=0;
                i++;
                s[i]++;
            }
            n--;  //数完一个之后n－－, 知道数完n个数
        }

    for(int i=19;i>=0;i--){
        if(out_turn == false){   //这个if是为了去掉最高位上的0,
                                     // 其中out_turn作为开关;
        if(s[i]==0)
        continue;
        else{
            out_turn=true;
            i++;
        }
        }
        else{  //输出转换之后的结果, 10输出A, 类推
        if(s[i]<10)
            System.out.print(s[i]);
        else
            System.out.print((char)('A'+(s[i]-10)));
        }
    }
    System.out.println();
    }
}
```
这种转换就像数数, 一共有多少个, 我来用十六进制的方法再数一遍, 逢16进1, 数完为止.这种方法的弊端就是效率低, 而且不能转换负数.

我们知道, 10进制的数据在计算机中使用2进制来存储的, 而16进制的出现也是为了阅读性强, 使4个位置放在一起计数.那么理论上, 用"位运算"来操作, 效率肯定会高.
```java
class turn10_16{
    public static void main(String[] args){
        int n=200; //n就是代转换的数字
        boolean out_turn=false; //输出时用, 去掉输出时候高位上的‘0’
        int[] s=new int[20]; //将转换后的十六进制数存放在s［］数组中

    for(int i=0;i<=8;i++){ //int型占用了8个byte位置, 每个byte即一个16进制,
                               //每次保留一个byte并且转换成16进制, 至少要8次(可以优化)
            int temp= n & 15;  //与0000-0000 0000-0000 0000-0000 0000-1111进
                               // 行&运算, 只保留最后4个位置即"个位"上的数
            s[i]=temp; //将这个数赋给个位
            n=n>>>4; //无符号右移4个位置, 再保留出十位上的数
        }

        for(int i=19;i>=0;i--){
            if(out_turn == false){   //这个if是为了去掉最高位上的0, 其中out_turn作为
                                     //开关;
                if(s[i]==0)
                    continue;
                else{
                    out_turn=true;
                    i++;
                }
            }
            else{  //输出转换之后的结果, 10输出A, 类推
                if(s[i]<10)
                    System.out.print(s[i]);
                else
                    System.out.print((char)('A'+(s[i]-10)));
            }
        }
        System.out.println();
    }
}
```

*2/2运算, 取绝对值运算, 取相反数运算等等, 直接对内存进行移位或者反码, 快了不少.

## 取余

对2的n次幂的取余操作可用位运算实现, 如`a % b`

使用位运算可以`a ^ b`, `a & ~8`, `a & (b - 1)`

由于语言优化的存在, 在实际使用时并没有太大的差异


# 应用

```Java
import static java.util.EnumSet.noneOf;
import static java.util.stream.Collectors.toCollection;

import java.util.EnumSet;
import java.util.Set;

public interface HasBitInfo {
    static <V extends Enum<V> & HasBitInfo> Set<V> toSet(long value, Class<V> type) {
        EnumSet<V> all = EnumSet.allOf(type);
        return all.stream()
                .filter(item -> (value & item.bitValue()) != 0)
                .collect(toCollection(() -> noneOf(type)));
    }

    static <V extends HasBitInfo> long toLong(Set<V> set) {
        return set.stream()
                .mapToLong(HasBitInfo::bitValue)
                .sum();
    }

    int bitIndex();

    default long bitValue() {
        return 1L << bitIndex();
    }
}
```