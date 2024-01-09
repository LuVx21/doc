<details>
<summary>Set结构层次</summary>
<!-- TOC -->

- [类](#类)
- [特征](#特征)
- [汇总](#汇总)

<!-- /TOC -->
</details>

## 类

![](https://cdn.nlark.com/yuque/0/2021/png/159888/1615862921510-f395cbf1-e2a7-4b23-952d-e909914b7774.png)

## 特征

* 元素无序(指插入和读取顺序一致), 可实现排序
* 不能重复
* 最多1个空值

## 汇总

* HashSet:以哈希码决定元素位置的set
* LinkedHashSet
* EnumSet:枚举类型专用Set,所有元素都是枚举类型.
* TreeSet:插入时会自动排序的set,但是如果中途修改元素大小,则不会再修改后重新排序,只会在插入时排序.

| 特性     | HashSet                        | LinkedHashSet                  | TreeSet                                 |
| :------- | :----------------------------- | :----------------------------- | :-------------------------------------- |
| 允许空   | ○                              | ○                              | ✘                                       |
| 允许重复 | ✘                              | ✘                              | ✘                                       |
| 有序     | ✘                              | ○                              | ○                                       |
| 线程安全 | ✘                              | ✘                              | ✘                                       |
| 父类     | AbstractSet                    | HashSet                        | AbstractSet                             |
| 接口     | Set<br/>Cloneable,Serializable | Set<br/>Cloneable,Serializable | NavigableSet<br/>Cloneable,Serializable |

> Set不保证插入有序是指Set这个接口的规范, 实现类只要遵循这个规范即可, 也能写出有序的set,如TreeSet