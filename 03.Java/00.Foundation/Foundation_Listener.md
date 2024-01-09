---
title: Java Listener
date: 2017-10-29
tags:
- Java
- Listener
- Web
---
<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [关于](#关于)
- [实现原理](#实现原理)
- [spring中使用](#spring中使用)
    - [顺序](#顺序)
    - [Spring 提供的事件](#spring-提供的事件)
- [servlet中的监听器](#servlet中的监听器)
- [应用场景](#应用场景)
- [参考](#参考)

<!-- /TOC -->
</details>

## 关于

* 事件源: 产生某个时间的地点, new一个事件的类就是事件源
* 事件: 监听器监听的对象
* 监听器: 监听到某个事件的创建, 开始设定的行为

## 实现原理

```Java
/**
* 事件源
*/
@Setter
public static class EventSource {
    private Listener listener;

    private Event click() {
        Event event = new Event(this);
        listener.listen(event);
        return event;
    }

    public void registerLister(Listener listener) {
        this.setListener(listener);
    }
}
/**
* 事件
*/
public static class Event extends EventObject {
    Event(Object source) {
        super(source);
    }
}
/**
* 监听器
*/
public static class Listener implements EventListener {
    private void listen(EventObject obj) {
        EventSource source = (EventSource) obj.getSource();
        System.out.println("监听到事件源:" + source);
    }
}
```

使用
```Java
EventSource source = new EventSource();
source.registerLister(new Listener());
source.click();
```

`Event`类的构造函数, 常见的传入是`this`, 指代的就是事件源

## spring中使用

`org.springframework.context.ApplicationEvent` 继承自`EventObject`
`org.springframework.context.ApplicationListener` 继承自`EventListener`

```Java
public class Event extends ApplicationEvent {
    private String name;
    public Event(Object source) {
        super(source);
    }
    public void click() {
    }
}
// 方式1
@Component
public class Listener1 implements ApplicationListener<Event> {
    @Override
    public void onApplicationEvent(Event event) {
        System.out.println(getClass().getSimpleName() + ":监听到事件:" + event.hashCode());
        event.click();
    }
}
// 方式2
@Component
public class Listener2 {
    @EventListener
    public void onApplicationEvent(Event event) {
        System.out.println(getClass().getSimpleName() + ":监听到事件:" + event.hashCode());
        event.click();
    }
}
```

触发事件:
```Java
@RestController
public class ListenerController {
    @Autowired
    ApplicationContext        applicationContext;
    @Autowired
    ApplicationEventPublisher publisher;

    @GetMapping(value = "/l")
    public String register() {
        applicationContext.publishEvent(new Event(this));
        publisher.publishEvent(new Event(this));
        return "成功";
    }
}
```
### 顺序

多个监听器间是没有先后顺序的, 如果想要某个监听器优先执行, 可以实现`SmartApplicationListener`接口

```Java
@Component
public class Listener3 implements SmartApplicationListener {
    /**
     * 判断事件类型用
     *
     * @param eventType
     * @return
     */
    @Override
    public boolean supportsEventType(Class<? extends ApplicationEvent> eventType) {
        return true;
    }

    /**
     * 判断事件源用
     *
     * @param sourceType
     * @return
     */
    @Override
    public boolean supportsSourceType(Class<?> sourceType) {
        return true;
    }

    /**
     * supportsEventType 和 supportsSourceType 方法同时返回true时才会执行此方法
     * 因此用于判断是否是需要监听的
     *
     * @param event
     */
    @Override
    public void onApplicationEvent(ApplicationEvent event) {
        System.out.println(getClass().getSimpleName() + ":监听到事件:" + event.hashCode());
        // ((Event) event).click();
    }

    /**
     * 保证顺序
     *
     * @return 值越小优先级越高
     */
    @Override
    public int getOrder() {
        return 0;
    }
}
```

### Spring 提供的事件

```Java
ApplicationReadyEvent extends SpringApplicationEvent
ServletWebServerInitializedEvent extends WebServerInitializedEvent
ApplicationStartedEvent extends SpringApplicationEvent
ContextRefreshedEvent extends ApplicationContextEvent
ContextClosedEvent extends ApplicationContextEvent
ContextStartedEvent extends ApplicationContextEvent
ContextStoppedEvent extends ApplicationContextEvent
```

## servlet中的监听器

作用:
监听web中的域对象(ServletContext,ServletRequest,HttpSession)

监听内容:

1. 监听三个对象的创建和销毁(生命周期)
2. 监听三个对象属性的变化
3. 监听session中javabean的状态

| 监听器                          |                                               |                                                      |
| :------------------------------ | :-------------------------------------------- | :--------------------------------------------------- |
| ServletContextListener          | 服务器启动                                    | 服务器关闭时或项目被移除时                           |
| ServletRequestListener          | 请求来时创建                                  | 响应生成时销毁                                       |
| HttpSessionListener             | 第一次调用request.getSession时或jsp访问时创建 | 1. session超时 2. 手动销毁session 3.服务器非正常关闭 |
| ServletContextAttributeListener |                                               |                                                      |
| ServletRequestAttributeListener |                                               |                                                      |
| HttpSessionAttributeListener    |                                               |                                                      |
| HttpSessionActivationListener   |序列化和反序列化| 钝化和活化                                           |
| HttpSessionBindingListener      |检测java是否添加到session或者从session中移除| 绑定和解绑                                           |

## 应用场景


## 参考

[自定义事件及监听](https://www.cnblogs.com/ashleyboy/p/9566579.html)


