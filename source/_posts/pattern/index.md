---
title: 设计模式
date: 2017-07-01
tags:
- 设计模式
---
<!-- TOC -->

- [[面向对象设计原则](Principle.md)](#面向对象设计原则principlemd)
- [Pattern](#pattern)
- [参考:](#参考)

<!-- /TOC -->

# [面向对象设计原则](Principle.md)

|设计原则|定义|使用频率|
|:---|:---|:---|
|单一职责原则 (Single Responsibility Principle, SRP)|一个类只负责一个功能领域中的相应职责|★★★★☆|
|开闭原则 (Open-Closed Principle, OCP)|软件实体应对扩展开放,而对修改关闭|★★★★★|
|里氏代换原则 (Liskov Substitution Principle, LSP)|所有引用基类对象的地方能够透明地使用其子类的对象|★★★★★|
|依赖倒转原则 (Dependence Inversion Principle, DIP)|要针对接口编程,而不是针对实现编程|★★★★★|
|接口隔离原则 (Interface Segregation Principle, ISP)|使用多个专门的接口,而不使用单一的总接口|★★☆☆☆|
|合成复用原则 (Composite Reuse Principle, CRP)|尽量使用对象组合,而不是继承来达到复用的目的|★★★★☆|
|迪米特法则 (Law of Demeter, LoD)|一个软件实体应当尽可能少地与其他实体发生相互作用|★★★☆☆|

# Pattern

分类方式:

1. 用途
* 创建型模式主要用于描述如何创建对象,
* 结构型模式主要用于描述如何实现类或对象的组合,
* 行为型模式主要用于描述类或对象怎样交互以及怎样分配职责
2. 处理对象(类or对象)
* 类模式
* 对象模式


|类型|模式名称|学习难度|使用频率|
|:---|:---|:---|:---|
|创建型模式(Creational)|[单例模式(Singleton)](单例模式.md)|2|8|
||[简单工厂模式(Simple Factory)*](简单工厂模式.md)|4|6|
||[工厂方法模式(Factory Method)](工厂方法模式.md)|4|10|
||[抽象工厂模式(Abstract Factory)](抽象工厂模式.md)|8|10|
||[原型模式(Prototype)](原型模式.md)|6|6|
||[建造者模式(Builder)](建造者模式.md)|8|4|
|结构型模式(Structural)|[适配器模式(Adapter)](适配器模式.md)|4|8|
||[桥接模式(Bridge)](桥接模式.md)|6|6|
||[组合模式(Composite)](组合模式.md)|6|8|
||[装饰模式(Decorator)](装饰模式.md)|6|6|
||[外观模式(Façade)](外观模式.md)|2|10|
||[享元模式(Flyweight)](享元模式.md)|8|2|
||[代理模式(Proxy)](代理模式.md)|6|8|
|行为型模式(Behavioral)|[职责链模式(Chain of Responsibility)](职责链模式.md)|6|4|
||[命令模式(Command)](命令模式.md)|6|8|
||[解释器模式(Interpreter)](解释器模式.md)|10|2|
||[迭代器模式(Iterator)](迭代器模式.md)|6|10|
||[中介者模式(Mediator)](中介者模式.md)|6|4|
||[备忘录模式(Memento)](备忘录模式.md)|4|4|
||[观察者模式(Observer)](观察者模式.md)|6|10|
||[状态模式(State)](状态模式.md)|6|6|
||[策略模式(Strategy)](策略模式.md)|2|8|
||[模板方法模式(Template Method)](模板方法模式.md)|4|6|
||[访问者模式(Visitor)](访问者模式.md)|8|2|



# 参考:

[1](https://gof.quanke.name)