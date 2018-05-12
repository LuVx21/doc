---
title: Java 基础
date: 2017-10-29
tags:
- Java
- Fileter
- Web
---

<!-- TOC -->

- [内部类](#内部类)

<!-- /TOC -->

# 内部类

在Java中，可以将一个类定义在另一个类里面或者一个方法里面，这样的类称为内部类。
为什么在Java中需要内部类？总结一下主要有以下四点：

1. 每个内部类都能独立的继承一个接口的实现，所以无论外部类是否已经继承了某个(接口的)实现，对于内部类都没有影响。内部类使得多继承的解决方案变得完整，
2. 方便将存在一定逻辑关系的类组织在一起，又可以对外界隐藏。
3. 方便编写事件驱动程序
4. 方便编写线程代码

个人觉得第一点是最重要的原因之一，内部类的存在使得Java的多继承机制变得更加完善。在这之前，Java 的继承机制主要由接口和单根继承实现，通过实现多个接口里的方法，看似能够实现多继承，但是并不总是高效的，因为一旦我们继承了一个接口就必然要实现它内部定义的所有方法。现在我们可以通过内部类多次继承某个具体类或者接口，省去一些不必要的实现动作。

先来看一段示例代码来方便理解利用内部类完善多继承机制：

```java
public class Father {
    public String powerFul = "市长";
}

public class Mother {
    public String wealthy = "一百万";
}
```

```java
public class Son {
    class Extends_Father extends Father{
    }

    class Extends_Mother extends Mother{
    }

    public void sayHello(){
        String father = new Extends_Father().powerFul;
        String mother = new Extends_Mother().wealthy;
        System.out.println("my father is:" + father + "my mother has:" + mother);
    }
}
```

显然，我们的 Son 类是不可能同时继承 Father 和 Mother 的，但是我们却可以通过在其内部定义内部类继承了 Father 和 Mother，必要的情况下，我们还能够重写继承而来的各个类的属性或者方法。
这就是典型的一种通过内部类实现多继承的实现方式，但是同时你也会发现，单单从 Son 来外表看，你根本不知道它内部多继承了 Father 和 Mother，从而往往会给我们带来一些错觉。所以你看，内部类并不绝对是一个好东西，它破坏了封装性，用的不好反而会适得其反，让你的程序一团糟，所以谨慎！

广泛意义上的内部类一般来说包括这四种：成员内部类、局部内部类、匿名内部类和静态内部类。下面就先来了解一下这四种内部类的用法。

**1. 成员内部类** 

成员内部类即内部类当做外部类得一个普通成员。

成员内部类可以无条件访问外部类得所有成员属性和成员方法

```java
class Circle {
    private double radius = 0;
    public static int count =1;
    public Circle(double radius) {
        this.radius = radius;
    }
     
    class Draw {     //内部类
        public void drawSahpe() {
            System.out.println(radius);  //外部类的private成员
            System.out.println(count);   //外部类的静态成员
        }
    }
}
```

不过要注意的是，当成员内部类拥有和外部类同名的成员变量或者方法时，会发生隐藏现象，即默认情况下访问的是成员内部类的成员。如果要访问外部类的同名成员，需要以下面的形式进行访问：

`外部类.this.成员方法`

`外部类.this.成员变量`

虽然成员内部类可以无条件地访问外部类的成员，而外部类想访问成员内部类的成员却不是这么随心所欲了。在外部类中如果要访问成员内部类的成员，必须先创建一个成员内部类的对象，再通过指向这个对象的引用来访问，由于成员内部类是依附于外部类而存在的，也就是说，如果要创建内部类的对象，前提是必须存在一个外部类的对象，方式如下所示：

```java
public class Test {
    public static void main(String[] args)  {
        //第一种方式：
        Outter outter = new Outter();
        Outter.Inner inner = outter.new Inner();  //必须通过Outter对象来创建
         
        //第二种方式：
        Outter.Inner inner1 = outter.getInnerInstance();
    }
}
 
class Outter {
    private Inner inner = null;
    public Outter() {
         
    }
     
    public Inner getInnerInstance() {
        if(inner == null)
            inner = new Inner();
        return inner;
    }
      
    class Inner {
        public Inner() {
             
        }
    }
}
```

**2. 局部内部类** 

局部内部类是定义在一个方法或者一个作用域里面的类，它和成员内部类的区别在于局部内部类的访问仅限于方法内或者该作用域内。

```java
class People{
    public People() {
         
    }
}
 
class Man{
    public Man(){
         
    }
     
    public People getWoman(){
        class Woman extends People{   //局部内部类
            int age =0;
        }
        return new Woman();
    }
}
```

注意，局部内部类就像是方法里面的一个局部变量一样，是不能有public、protected、private以及static修饰符的。

**3. 匿名内部类**

匿名内部类，顾名思义，是没有名字的类，那么既然它没有名字，自然也就无法显式的创建出其实例对象了，所以匿名内部类适合那种只使用一次的情境,匿名内部类是唯一一种没有构造器的类。

典型得匿名内部类使用例子如下：

```java
public class Test{
    public static void main(String[] args){
        Object obj=new Object(){
            @Override
            public String toString(){
                return "hello world";
            }
        }
    }
}
```

上述代码效果等同于如下代码：

```java
public class MyObj extends Object{
    @Override 
    public String toString(){
        return "hello world";
    }
}
```

```java
public static void main(String[] args){
    Object obj = new MyObj();
}
```

为了一个只使用一次的类而单独创建一个 .java 文件，是否有些浪费和繁琐？
在我看来，匿名内部类最大的好处就在于能够简化代码块。

匿名内部类往往是对某个父类或者接口的继承与实现,匿名类的基本使用语法格式如下：

```java
new 父类/接口{
    //匿名类的实现
}
```
常用场景如自定义比较器实现规则排序：

```java
 Arrays.sort(data, new Comparator<Integer>(){
            public int compare(Integer a,Integer b)
            {
                return (a+""+b).compareTo(b+""+a);//构建ab的比较器
            }
        });
```

**4. 静态内部类** 

静态内部类也是定义在另一个类里面的类，只不过在类的前面多了一个关键字static。静态内部类是不需要依赖于外部类的，这点和类的静态成员属性有点类似，并且它不能使用外部类的非static成员变量或者方法，这点很好理解，因为在没有外部类的对象的情况下，可以创建静态内部类的对象，如果允许访问外部类的非static成员就会产生矛盾，因为外部类的非 static 成员必须依附于具体的对象。

常见使用场景如，静态内部类实现单例模式：

```java
/**
 * 懒汉式单例类线程安全可可能有问题，性能会受到影响
 * 饿汉式不能实现延迟加载，不管将来用不用始终占据内存
 * 内部类的实现方式可以很好的解决上面的缺点
 */
public class InnerClassSingleton {
    private static class SingletonHolder{
        private static final InnerClassSingleton SINGLETON=new InnerClassSingleton();
    }
    private InnerClassSingleton() {}
    public static final InnerClassSingleton getSingleton(){
        return SingletonHolder.SINGLETON;
    }
}
```

此外，我们 Integer 类中的 IntegerCache 就是一个静态的内部类，它不需要访问外围类中任何成员，却通过内部定义的一些属性和方法为外围类提供缓存服务。

> [Java内部类详解](https://www.cnblogs.com/latter/p/5665015.html)