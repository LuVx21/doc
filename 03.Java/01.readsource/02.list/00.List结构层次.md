<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [类](#类)
- [特征](#特征)
- [汇总](#汇总)

<!-- /TOC -->
</details>

## 类

![](https://cdn.nlark.com/yuque/0/2020/png/159888/1592208047004-d2a45092-bb6a-4df7-b64a-570000d47a88.png)

## 特征

* ArrayList: 动态数组
* LinkedList: 双向链表, 同时也实现了双向链表
* Vector: 线程安全的动态数组
* Stack: 对象栈,Vector的子类,遵循先进后出的原则

## 汇总

实现了 Collection 和 Iterable 接口

* 元素有序
* 可以重复
* 可以为空
* 可以根据index精确访问

| 特性     | ArrayList                                    | Vector                                       | LinkedList                            |
| :------- | :------------------------------------------- | -------------------------------------------- | :------------------------------------ |
| 允许空   | ○                                            | ○                                            | ○                                     |
| 允许重复 | ○                                            | ○                                            | ○                                     |
| 有序     | ○                                            | ○                                            | ○                                     |
| 线程安全 | ✘                                            | ○                                            | ✘                                     |
| 父类     | AbstractList                                 | AbstractList                                 | AbstractSequentialList                |
| 接口     | List,RandomAccess<br/>Cloneable,Serializable | List,RandomAccess<br/>Cloneable,Serializable | List,Deque<br/>Cloneable,Serializable |

> 有序的意思是遍历数据的顺序和存放数据的顺序是否一致



