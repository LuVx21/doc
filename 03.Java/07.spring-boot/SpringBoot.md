<details>
<summary>点击展开目录</summary>
<!-- TOC -->

    - [打包](#打包)
    - [启动](#启动)
    - [关闭](#关闭)
- [注解](#注解)
    - [@Controller](#controller)
    - [@Value](#value)
        - [array/list](#arraylist)
    - [@Bean](#bean)
    - [@PostConstruct](#postconstruct)
    - [@Configuration](#configuration)
    - [@ConfigurationProperties](#configurationproperties)
    - [@RequestParam](#requestparam)
    - [@PathVariable](#pathvariable)
    - [@RequestBody](#requestbody)
    - [@Profile](#profile)
    - [@ControllerAdvice](#controlleradvice)
    - [@ExceptionHandler](#exceptionhandler)
    - [@InitBinder](#initbinder)
    - [@ModelAttribute](#modelattribute)
    - [@Primary/@Qualifier](#primaryqualifier)
    - [@ResponseBody](#responsebody)
- [Tips](#tips)

<!-- /TOC -->
</details>

## 打包

## 启动

## 关闭

pom.xml:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

application.properties:
```conf
#启用shutdown
endpoints.shutdown.enabled=true
#禁用密码验证
endpoints.shutdown.sensitive=false
```

curl -X POST http://localhost:8080/shutdown

> https://blog.csdn.net/quincuntial/article/details/54410916

mvn package -P prod
mvn spring-boot:run -Dspring.profiles.active=dev

# 注解

## @Controller

如果你需要在Spring4之前开发 RESTful Web服务的话, 你需要使用@Controller 并结合@ResponseBody注解, 也就是说@Controller +@ResponseBody= @RestController(Spring 4 之后新加的注解)

https://dzone.com/articles/spring-framework-restcontroller-vs-controller

## @Value

```conf
org.luvx.title=LuVx
org.luvx.description=Sample of Spring-Boot
```
```Java
@Component
public class Ren {
    @Value("${org.luvx.title}")
    private String title;

    private static String description;

    @Value("${org.luvx.description}")
    public void setDescription(String description) {
        Ren.description = description;
    }

    @PostConstruct
    public void run() {
        System.out.println(title);
        System.out.println(description);
    }
}
```

### array/list

```Java
@Value("#{'${approve.cclist}'.split(',')}")
private List<String> ccList;
@Value("${approve.cclist:1,2,3}")
private String[] ccList;
```

## @Bean

## @PostConstruct


## @Configuration

classpath:/config


## @ConfigurationProperties

## @RequestParam

## @PathVariable

## @RequestBody

## @Profile

## @ControllerAdvice

## @ExceptionHandler

## @InitBinder

## @ModelAttribute 


## @Primary/@Qualifier
当你一个接口的实现类有多个的时候, 你通过@Component来注册你的实现类有多个, 但是在注入的时候使用@Autowired
@Primary	优先方案, 被注解的实现, 优先被注入
@Qualifier	先声明后使用, 相当于多个实现起多个不同的名字, 注入时候告诉我你要注入哪个


## @ResponseBody

# Tips

MyBatis控制台输出sql语句

```conf
mybatis.configuration.log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```


jar包的所在位置
```Java
//第一种
File path = new File(ResourceUtils.getURL("classpath:").getPath());
if (!path.exists()) path = new File("");
System.out.println(path.getAbsolutePath());
//第二种
System.out.println(System.getProperty("user.dir"));
//第三种
String path1 = ClassUtils.getDefaultClassLoader().getResource("").getPath();
System.out.println(URLDecoder.decode(path1, "utf-8"));
//第四种
String path2 = ResourceUtils.getURL("classpath:").getPath();
System.out.println(path2);
//第五种
ApplicationHome h = new ApplicationHome(getClass());
File jarF = h.getSource();
File jarF = h.getDir();
System.out.println(jarF.getParentFile().toString());
```
> 使用第5种


