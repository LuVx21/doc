---
title: 拦截器
date: 2017-10-29
tags:
- Java
- Web
---
<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [参考](#参考)

<!-- /TOC -->
</details>


```Java
public class Interceptor1 implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {
        System.out.println(getClass().getSimpleName() + ":拦截到请求:" + request);
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)
            throws Exception {
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
            throws Exception {
    }
}
public class Interceptor2 extends HandlerInterceptorAdapter {
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)
            throws Exception {
    }
}
@Configuration
public class InterceptorConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new Interceptor1())
                .addPathPatterns("/**")
                .order(1);

        registry.addInterceptor(new Interceptor2())
                .addPathPatterns("/**)
                .order(2);
    }
}
```

> 拦截器也可以继承`HandlerInterceptorAdapter`, 为`HandlerInterceptor`子接口的抽象类, 可以避免重载不需要的方法
> 配置类也可以继承自 `WebMvcConfigurerAdapter`, 但在spring5被废弃
> 指定拦截器的执行顺序, 数值越小越优先

## 参考

[](https://blog.csdn.net/reggergdsg/article/details/52962774)


