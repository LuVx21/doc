---
title: 排序算法
date: 2015-04-15
tags:
- Java
---
<!-- TOC -->

- [内部排序](#内部排序)
    - [交换排序](#交换排序)
        - [冒泡排序(Bubble Sort)](#冒泡排序bubble-sort)
        - [快速排序(Quick Sort)](#快速排序quick-sort)
    - [插入排序](#插入排序)
        - [直接插入排序(Insertion Sort)](#直接插入排序insertion-sort)
        - [希尔排序(Shell Sort)](#希尔排序shell-sort)
    - [选择排序](#选择排序)
        - [直接选择排序(Selection Sort)](#直接选择排序selection-sort)
        - [堆排序](#堆排序)
    - [归并排序](#归并排序)
- [外部排序](#外部排序)
- [性能分析](#性能分析)

<!-- /TOC -->

面试准备而做出的排序算法总结,供查阅用

# 内部排序

## 交换排序

### 冒泡排序(Bubble Sort)

从左向右进行两两比较,若当前元素大于其后一个元素,则发生交换.
外层循环由元素的个数控制,内层循环由比较的次数控制,
外层每循环一次,确定出当次循环的最大值,n次循环后,前n个大数被确定下来,即每次的比较次数在递减

```Java
public static void bubbleSort(int[] array) {
    int length = array.length;
    for (int i = 0; i < length; i++) {
        for (int j = 1; j < length - i; j++) {
            if (array[j - 1] > array[j]) {
                int temp = array[j];
                array[j] = array[j - 1];
                array[j - 1] = temp;
            }
        }
    }
}
```

> 最佳情况：T(n) = O(n)   最差情况：T(n) = O(n<sup>2</sup>)   平均情况：T(n) = O(n<sup>2</sup>)


### 快速排序(Quick Sort)

每次排序将待排序内容分为2个部分,其中一部分总是比另一部分小


```Java

```

## 插入排序

### 直接插入排序(Insertion Sort)

将当前元素与其之前的元素进行比较,比该元素大,则依次后移,直至找到比该元素小的元素,然后将该元素插入在其之后.

```Java
public static void insertionSort(int[] array) {
    int length = array.length;
    for (int i = 1; i < length; i++) {
        int temp = array[i];
        int j = i - 1;
        while (j >= 0 && (array[j] > temp)) {
            array[j + 1] = array[j];
            j--;
        }

        array[j + 1] = temp;
    }
}
```

> 最佳情况：T(n) = O(n)   最坏情况：T(n) = O(n<sup>2</sup>)   平均情况：T(n) = O(n<sup>2</sup>)

### 希尔排序(Shell Sort)

是一种分组策略和直接插入排序的组合,一般先分n/2组,对每组进行插入排序,
组内有序后,对整体重新进行分组,通常变为(n/2)/2组,依次类推.

```Java
public static void shellSort(int[] array) {
    int length = array.length;
    // 分割的组数
    int groups = 2;
    int nums = length / groups;
    while (nums >= 1) {
        // 循环的次数和组数相同
        for (int i = 0; i < groups; i++) {
            for (int j = i + nums; j < length; j += nums) {
                int temp = array[j];
                int k = j - nums;
                // 右移数据用循环
                while (k >= i && array[k] > temp) {
                    array[k + nums] = array[k];
                    k -= nums;
                }
                // 插入数据
                array[k + nums] = temp;
            }
        }
        nums = nums / 2;
    }
}
```
> 最佳情况：T(n) = O(nlog<sub>2</sub> n)  最坏情况：T(n) = O(nlog<sub>2</sub> n)  平均情况：T(n) =O(nlog n)　

## 选择排序

### 直接选择排序(Selection Sort)

首先在代排序序列中找到最小元素,放在起始处,然后找到第二小元素,放在起始处之后,依次类推.

```Java
public static void selectSort(int[] array) {
    int length = array.length;
    int minIndex;
    for (int i = 0; i < length - 1; i++) {
        minIndex = i;
        for (int j = i + 1; j < length; j++) {
            if (array[j] < array[minIndex]) {
                minIndex = j;
            }
        }
        if (minIndex != i) {
            int temp = array[i];
            array[i] = array[minIndex];
            array[minIndex] = temp;
        }
    }
}
```

> 最佳情况：T(n) = O(n<sup>2</sup>)  最差情况：T(n) = O(n<sup>2</sup>)  平均情况：T(n) = O(n<sup>2</sup>)

### 堆排序



## 归并排序


# 外部排序




# 性能分析


排序算法的优劣取决于数据比较的次数和移动的次数

怎么判断一组数据是否有序


https://www.cnblogs.com/onepixel/articles/7674659.html
