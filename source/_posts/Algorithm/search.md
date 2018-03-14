---
title: 搜索算法
date: 2015-04-30
tags:
- Java
---

<!-- TOC -->

- [顺序查找](#顺序查找)
- [二分查找](#二分查找)
- [插值查找](#插值查找)
- [斐波那契查找](#斐波那契查找)
- [树表查找](#树表查找)
- [分块查找](#分块查找)
- [哈希查找](#哈希查找)
- [参考](#参考)

<!-- /TOC -->

常见查找算法的实现


# 顺序查找

```Java
public static int SequenceSearch(int[] array,int key){
    int length = array.length;
    for(int i = 0;i < length;i++){
        if(array[i] == key){
            return i;
        }
    }
    return -1;
}
```

# 二分查找

针对有序的序列

```Java
public static int binSearch(int[] array,int key){
    int start = 0;
    int end = array.length - 1;
    while(start < end){
        int middle = (start + end)/2;
        if(array[middle] == key){
            return middle;
        }
        if(array[middle] > key){
            end = middle;
        }
        if(array[middle] < key){
            start = middle;
        }
    }
    return -1;
}
```

# 插值查找

# 斐波那契查找

# 树表查找

# 分块查找

# 哈希查找




# 参考

[七大查找算法](http://blog.jobbole.com/111629/)