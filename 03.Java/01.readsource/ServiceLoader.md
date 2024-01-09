<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [关于](#关于)
- [使用](#使用)
- [适用场景](#适用场景)
- [参考](#参考)

<!-- /TOC -->
</details>

## 关于
SPI: Service Provider Interface, JDK内置的服务提供发现机制

* 对接口的实现进行动态替换
* 降低依赖

## 使用

```Java
package org.luvx.service;
public interface Service {
    void run();
}
package org.luvx.service.impl;
public class SimpleService implements Service {
    @Override
    public void run() {
        System.out.println("service loader调用");
    }
}
```
`META-INF/services/`目录下创建`org.luvx.service.Service`文件, 内容为`org.luvx.service.impl.SimpleService`

那么在使用时, 即可如下:
```Java
ServiceLoader<Service> serviceLoader = ServiceLoader.load(Service.class);
for (Service service : serviceLoader) {
    service.run();
}
```

## 适用场景

实际上是`基于接口的编程＋策略模式＋配置文件`组合实现的动态加载机制

优点:

使用者面向接口编程, 不用关心第三方对这个接口是怎样实现的

如 JDBC数据库驱动的实现

找到一个`mysql-connector-java-5.1.44.jar`文件, 解压后在`META-INF/services`目录下有一个名为`java.sql.Driver`的文件

其内容:
```java
com.mysql.jdbc.Driver
com.mysql.fabric.jdbc.FabricMySQLDriver
```

这样的数据库驱动包就不需要使用`Class.forName("com.mysql.jdbc.Driver")`来注册驱动

其实现原理即是使用了SPI机制

`DriverManager`的源码中:

```Java
static {
    loadInitialDrivers();
    println("JDBC DriverManager initialized");
}
```
`loadInitialDrivers()`内部:

```Java
public void run() {
    ServiceLoader<Driver> loadedDrivers = ServiceLoader.load(Driver.class);
    Iterator<Driver> driversIterator = loadedDrivers.iterator();

    try{
        while(driversIterator.hasNext()) {
            driversIterator.next();
        }
    } catch(Throwable t) {
    }
    return null;
}
```

## 参考

