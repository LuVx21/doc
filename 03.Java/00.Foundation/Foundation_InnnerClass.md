<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [内部类及作用](#内部类及作用)
- [内部类的定义](#内部类的定义)
    - [成员内部类](#成员内部类)
    - [静态内部类](#静态内部类)
    - [局部内部类](#局部内部类)
    - [匿名内部](#匿名内部)
- [QA](#qa)
- [参考](#参考)

<!-- /TOC -->
</details>

## 内部类及作用

前面不能有访问类型修饰符，但前面可以使用final或abstract修饰符。
这种内部类对其他类是不可见的其他类无法引用这种内部类，但是这种内部类创建的实例对象可以传递给其他类访问。
这种内部类必须是先定义，后使用，即内部类的定义代码必须出现在使用该类之前，这与方法中的局部变量必须先定义后使用的道理也是一样的。
这种内部类可以访问方法体中的局部变量，但是，该局部变量前必须加final修饰符

Java中指定义在类内部的一个类, 可以定义在外部类的方法外面, 也可以定义在方法体中(此时被称为局部内部类), 此时可以使用final或abstract修饰
还可以声明匿名内部类, 通常是`new interface(){}`形式

作用:
* 实现多重继承:声明多个继承了其他类的内部类, 从而可以使用这些内部类的成员, 间接实现多重继承.
* 更好的实现封装:如私有化内部类, 若想使用必须通过外部类对象进行实例化.
* 获取更多的外部类的访问权限:内部类可以使用外部类所有属性和方法.
* 区分父类和接口中的同名方法:如同时存在继承和实现时, 怎样区分两者中的同名方法?可以通过内部类实现接口解决

为什么在Java中需要内部类? 总结一下主要有以下四点:

1. 每个内部类都能独立的继承一个接口的实现, 所以无论外部类是否已经继承了某个(接口的)实现, 对于内部类都没有影响. 内部类使得多继承的解决方案变得完整,
2. 方便将存在一定逻辑关系的类组织在一起, 又可以对外界隐藏.
3. 方便编写事件驱动程序, 方便编写线程代码
5. 可以将内部类的访问控制在`private`, 提高了安全性

个人觉得第一点是最重要的原因之一, 内部类的存在使得Java的多继承机制变得更加完善. 在这之前, Java 的继承机制主要由接口和单根继承实现, 通过实现多个接口里的方法, 看似能够实现多继承, 但是并不总是高效的, 因为一旦我们继承了一个接口就必然要实现它内部定义的所有方法(Java8开始有所改变, 此处不详述). 现在我们可以通过内部类多次继承某个具体类或者接口, 省去一些不必要的实现动作.

> 普通内部类和静态内部类能被继承
> 内部类不会被重写:即父子类中有同名的内部类, 各自的该内部类属于各自.

先来看一段示例代码来方便理解利用内部类完善多继承机制:

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
    class Extends_Father extends Father {
    }

    class Extends_Mother extends Mother {
    }

    public void sayHello() {
        String father = new Extends_Father().powerFul;
        String mother = new Extends_Mother().wealthy;
        System.out.println("my father is:" + father + "my mother has:" + mother);
    }
}
```

显然, 我们的 Son 类是不可能同时继承 Father 和 Mother 的, 但是我们却可以通过在其内部定义内部类继承了 Father 和 Mother, 必要的情况下, 我们还能够重写继承而来的各个类的属性或者方法.

这就是典型的一种通过内部类实现多继承的实现方式, 但是同时你也会发现, 单单从 Son 来外表看, 你根本不知道它内部多继承了 Father 和 Mother, 从而往往会给我们带来一些错觉. 所以你看, 内部类并不绝对是一个好东西, 它破坏了封装性, 用的不好反而会适得其反, 让你的程序一团糟, 所以谨慎！

---

普通内部类:

* 使用依赖于外部类实例, 其本身持有外部类的实例
* 其内部不可以定义静态变量或方法, 非静态的可以(普通内部类不一定随外部类一起加载)
* 可以使用外部静态属性, 方法, 也可以使用外部非静态属性, 方法, 实质是内部类中存在一个final修饰的名为`this$0`的外部实例

静态内部类:

* 其对象的使用不依赖于外部类实例,
* 内部可以定义非静态变量, 方法, 但若想使用此变量, 方法, 内部类需要实例化
* 也可以定义静态, 方法, 使用不需要实例化.
* 只能使用外部静态属性, 方法.不可以使用外部非静态属性, 方法

## 内部类的定义

* 可以被`public`, `protected`, `private`等修饰控制访问
* 也可以被`abstract`修饰以达到继承或实现的目的
* 可以被`static`, `final`修饰
* 也可以继承或实现自其它类

广泛意义上的内部类一般来说包括这四种: 成员内部类, 局部内部类, 静态内部类和匿名内部类.

其中最常用的是成员内部类和静态内部类, 下面就先来了解一下这四种内部类的用法.

### 成员内部类

成员内部类即内部类当做外部类得一个普通成员.

成员内部类可以无条件访问外部类得所有成员属性和成员方法

```java
class Circle {
    private double radius = 0;
    public static int count = 1;
    public Circle(double radius) {
        this.radius = radius;
    }

    class Draw { //内部类
        public void drawSahpe() {
            System.out.println(radius); //外部类的private成员
            System.out.println(count); //外部类的静态成员
        }
    }
}
```

不过要注意的是, 当成员内部类拥有和外部类同名的成员变量或者方法时, 会发生隐藏现象, 即默认情况下访问的是成员内部类的成员. 如果要访问外部类的同名成员, 需要以下面的形式进行访问:

`外部类.this.成员变量/方法`

虽然成员内部类可以无条件地访问外部类的成员, 而外部类想访问成员内部类的成员却不是这么随心所欲了.

在外部类中如果要访问成员内部类的成员, 必须先创建一个成员内部类的对象, 再通过指向这个对象的引用来访问,

由于成员内部类是依附于外部类而存在的, 也就是说, 如果要创建内部类的对象, 前提是必须存在一个外部类的对象, 方式如下所示:

```java
public class Test {
    public static void main(String[] args)  {
        //第一种方式:
        Outter outter = new Outter();
        Outter.Inner inner = outter.new Inner(); //必须通过Outter对象来创建

        //第二种方式:
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

### 静态内部类

静态内部类也是定义在另一个类里面的类, 和普通内部类相比, 仅仅多了`static`修饰.

静态内部类是不需要依赖于外部类的, 这点和类的静态成员属性有点类似, 并且它不能使用外部类的非static成员变量或者方法,

编译出class文件存在两个: `Outer.class`和`Outer$Inner.class`

常见使用场景如, 静态内部类实现单例模式:

```java
/** 懒汉式单例类线程安全可能有问题, 性能会受
 * 饿汉式不能实现延迟加载, 不管将来用不用始终占据内存
 * 内部类的实现方式可以很好的解决上面的缺点
 */
public class InnerClassSingleton {
    private static class SingletonHolder {
        private static final InnerClassSingleton SINGLETON = new InnerClassSingleton();
    }
    private InnerClassSingleton() {}
    public static final InnerClassSingleton getSingleton() {
        return SingletonHolder.SINGLETON;
    }
}
```

此外, 我们 Integer 类中的 IntegerCache 就是一个静态的内部类, 它不需要访问外围类中任何成员, 却通过内部定义的一些属性和方法为外围类提供缓存服务.

### 局部内部类

局部内部类是定义在一个方法或者一个作用域里面的类, 它和成员内部类的区别在于局部内部类的访问仅限于方法内或者该作用域内.

```java
class People {
}

class Man {
    public People getWoman() {
        // 局部内部类
        class Woman extends People {
            int age = 0;
        }
        return new Woman();
    }
}
```

注意, 局部内部类就像是方法里面的一个局部变量一样, 是不能有public, protected, private以及static修饰符的.

### 匿名内部

匿名内部类, 顾名思义, 是没有名字的类, 那么既然它没有名字, 自然也就无法显式的创建出其实例对象了,

所以匿名内部类适合那种只使用一次的情境, 匿名内部类是唯一一种没有构造方法的类.

典型得匿名内部类使用例子如下:

```java
public class Test {
    public static void main(String[] args) {
        Object obj = new Object() {
            @Override
            public String toString() {
                return "hello world";
            }
        }
    }
}
```

上述代码效果等同于如下代码:

```java
public class MyObj extends Object {
    @Override
    public String toString() {
        return "hello world";
    }
}
```

```java
public static void main(String[] args) {
    Object obj = new MyObj();
}
```

为了一个只使用一次的类而单独创建一个 .java 文件, 是否有些浪费和繁琐?
在我看来, 匿名内部类最大的好处就在于能够简化代码块.

匿名内部类往往是对某个父类或者接口的继承与实现, 匿名类的基本使用语法格式如下:

```java
new 父类/接口 {
 //匿名类的实现
}
```
常用场景如自定义比较器实现规则排序:

```java
Arrays.sort(data, new Comparator<Integer>() {
    public int compare(Integer a, Integer b) {
        return (a+""+b).compareTo(b+""+a);//构建ab的比较器
    }
});
```

## QA

**闭包和局部内部类的区别**

简单来说, 闭包实现的是将一个方法作为一个变量去存储,
Java中的闭包是通过`接口+内部类`实现, 因为内部类能够访问外部类的所有属性和方法, 同时实现接口中的方法

**静态内部类的设计意图**

保证封装的完整性, 不需要在外部引用, 只为当前外部类提供服务

## 参考

1. [Java内部类详解](https://www.cnblogs.com/latter/p/5665015.html)
