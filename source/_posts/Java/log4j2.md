---
title: log4j2
tags:
- Java
---

# log4j2 使用详解

@(JavaEE)


[TOC]

```java
private static Logger logger = LogManager.getLogger(MyApp.class.getName());
```

和log4j的1个不同就是.jar有2个

* log4j-core-xx.jar
* log4j-api-xx.jar


trace： 是追踪，就是程序推进以下，你就可以写个trace输出，所以trace应该会特别多，不过没关系，我们可以设置最低日志级别不让他输出。

　　　　debug： 调试么，我一般就只用这个作为最低级别，trace压根不用。是在没办法就用eclipse或者idea的debug功能就好了么。

　　　　info： 输出一下你感兴趣的或者重要的信息，这个用的最多了。

　　　　warn： 有些信息不是错误信息，但是也要给程序员的一些提示，类似于eclipse中代码的验证不是有error 和warn（不算错误但是也请注意，比如以下depressed的方法）。

　　　　error： 错误信息。用的也比较多。

　　　　fatal： 级别比较高了。重大错误，这种级别你可以直接停止程序了，是不应该出现的错误么！不用那么紧张，其实就是一个程度的问题

```
public class Log4j2 {
	private static Logger logger = LogManager.getLogger(Log4j2.class.getName());

	public static boolean hello() {
		logger.entry(); // trace级别的信息，单独列出来是希望你在某个方法或者程序逻辑开始的时候调用，和logger.trace("entry")基本一个意思

		logger.trace("trace");
		logger.debug("debug");
		logger.info("info");
		logger.warn("warn");
		logger.error("error");
		logger.fatal("fatal");

		logger.log(Level.DEBUG, "debug"); // 这个就是制定Level类型的调用

		logger.exit(); // 和entry()对应的结束方法，和logger.trace("exit");一个意思
		return false;
	}

	public static void main(String[] args) {
		System.out.println("---------------------------");
		Log4j2.hello();
		System.out.println("---------------------------");
	}

}
```

配置文件只能采用.xml, .json或者 .jsn。在默认情况下，系统选择configuration文件的优先级如下：（classpath为scr文件夹）

classpath下名为 log4j-test.json 或者log4j-test.jsn文件
classpath下名为 log4j2-test.xml
classpath下名为 log4j.json 或者log4j.jsn文件
classpath下名为 log4j2.xml

Log4J传统的配置一直是.properties文件，键值对的形式





　1）根节点configuration，然后有两个子节点：appenders和loggers（都是复数，意思就是可以定义很多个appender和logger了）（如果想详细的看一下这个xml的结构，可以去jar包下面去找xsd文件和dtd文件）

　　2）appenders：这个下面定义的是各个appender，就是输出了，有好多类别，这里也不多说（容易造成理解和解释上的压力，一开始也未必能听懂，等于白讲），先看这个例子，只有一个Console，这些节点可不是随便命名的，Console就是输出控制台的意思。然后就针对这个输出设置一些属性，这里设置了PatternLayout就是输出格式了，基本上是前面时间，线程，级别，logger名称，log信息等，差不多，可以自己去查他们的语法规则。

　　3）loggers下面会定义许多个logger，这些logger通过name进行区分，来对不同的logger配置不同的输出，方法是通过引用上面定义的logger，注意，appender-ref引用的值是上面每个appender的name，而不是节点名称。