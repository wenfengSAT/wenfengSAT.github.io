---
layout:     post
title:      SpringBoot配置类WebMvcConfigurerAdapter
subtitle:   SpringBoot配置类WebMvcConfigurerAdapter
date:       2018-09-04
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - SpringBoot
---

>SpringBoot配置类WebMvcConfigurerAdapter

```java
WebMvcConfigurerAdapter配置类其实是Spring内部的一种配置方式，
采用JavaBean的形式来代替传统的xml配置文件形式进行针对框架个性化定制，
下面我们来看一下该类内的常用方法。
```
>本章目标

```java
继承WebMvcConfigurerAdapter采用JavaBean形式实现个性化配置定制。
```

>构建项目

{% raw %}
```hbs
本章内容同样不涉及到业务逻辑，我们创建一个web项目即可，pom.xml配置文件如下所示：

...//省略
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
 
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
            <!--<scope>provided</scope>-->
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
...//省略
```
{% endraw %}

>WebMvcConfigurerAdapter实现类

我们创建一个配置实体类型，并继承WebMvcConfigurerAdapter，代码如下所示：

```java
package com.yuqiyu.chapter34;
 
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.*;
 
import java.util.List;
 
/**
 * 自定义配置类实现JavaBean注解形式配置
 * ========================
 * Created with IntelliJ IDEA.
 * User：恒宇少年
 * Date：2017/9/3
 * Time：21:48
 * 码云：http://git.oschina.net/jnyqy
 * ========================
 */
@Configuration
public class WebConfiguration
    extends WebMvcConfigurerAdapter
{
}
```
我们在配置类上添加了注解@Configuration，标明了该类是一个配置类并且会将该类
作为一个SpringBean添加到IOC容器内，我们打开该注解的源码查看如下所示：

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//
 
package org.springframework.context.annotation;
 
import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import org.springframework.stereotype.Component;
 
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration {
    String value() default "";
}
```

```java
可以看到在@Configuration 上声明式添加了Spring注入注解@Component，
也就是解释了为什么我们配置了@Configuration会被自动添加到IOC容器内。

WebMvcConfigurerAdapter该抽象类其实里面没有任何的方法实现，
只是空实现了接口WebMvcConfigurer内的全部方法，并没有给出任何的业务逻辑处理，
这一点设计恰到好处的让我们不必去实现那些我们不用的方法，都交由
WebMvcConfigurerAdapter抽象类空实现，如果我们需要针对具体的某一个方法做出
逻辑处理，仅仅需要在WebMvcConfigurerAdapter子类中@Override对应方法就可以了。
```
>配置拦截器

在之前Xml配置形式天下的时候，我们都是在spring-mvc.xml配置文件内添加<mvc:interceptor>
标签配置拦截器。拦截器的相关创建请访问第六章：如何在SpringBoot项目中使用拦截器，拦截器配置如下所示：

```java
 /**
     * 拦截器配置
     * @param registry
     */
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        super.addInterceptors(registry);
        registry.addInterceptor(new TestInterceptor()).addPathPatterns("/**");
    }
```

InterceptorRegistry 内的addInterceptor需要一个实现HandlerInterceptor接口的拦截器实例，
addPathPatterns方法用于设置拦截器的过滤路径规则。

>配置CORS

跨域我们之前章节也有讲到，请访问第二十五章：SpringBoot添加支持CORS跨域访问，
Spring既然为了集成了CROS，那就证明了一点，以后前后端分离是一个开发趋势，配置代码如下所示：

```java
/**
     * 跨域CORS配置
     * @param registry
     */
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        super.addCorsMappings(registry);
        registry.addMapping("/cors/**")
                .allowedHeaders("*")
                .allowedMethods("POST","GET")
                .allowedOrigins("*");
    }
```

>配置ViewController

这一个配置在之前是经常被使用到的，最经常用到的就是"/"、"/index"路径请求时不通过@RequestMapping配置，
而是直接通过配置文件映射指定请求路径到指定View页面，
当然也是在请求目标页面时不需要做什么数据处理才可以这样使用，配置内容如下所示：

```java
/**
     * 视图控制器配置
     * @param registry
     */
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        super.addViewControllers(registry);
        registry.addViewController("/").setViewName("/index");
    }
```

>配置ViewResolver

这个对我们来说很熟悉，只要我们配置html、Jsp页面视图时就会用到InternalResourceViewResolver
配置类，然后设置preffix、suffix参数进行配置视图文件路径前缀与后缀。配置代码如下所示：

```java
/**
     * 配置请求视图映射
     * @return
     */
    @Bean
    public InternalResourceViewResolver resourceViewResolver()
    {
        InternalResourceViewResolver internalResourceViewResolver = new InternalResourceViewResolver();
        //请求视图文件的前缀地址
        internalResourceViewResolver.setPrefix("/WEB-INF/jsp/");
        //请求视图文件的后缀
        internalResourceViewResolver.setSuffix(".jsp");
        return internalResourceViewResolver;
    }
 
    /**
     * 视图配置
     * @param registry
     */
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        super.configureViewResolvers(registry);
        registry.viewResolver(resourceViewResolver());
        /*registry.jsp("/WEB-INF/jsp/",".jsp");*/
    }
```

上述代码中方法resourceViewResolver上配置了@Bean注解，该注解会将方法返回值加入到SpringIoc容器内。
而在configureViewResolvers方法内配置视图映射为resourceViewResolver方法返回的
InternalResourceViewResolver实例，这样完成了视图的配置。在下面还有注释掉的一部分代码，
这块代码很神奇，我们先来看看org.springframework.web.servlet.config.annotation.ViewResolverRegistry源码：

```java
package org.springframework.web.servlet.config.annotation;
 
public class ViewResolverRegistry {
    ...//省略代码
    public UrlBasedViewResolverRegistration jsp() {
        return this.jsp("/WEB-INF/", ".jsp");
    }
 
    public UrlBasedViewResolverRegistration jsp(String prefix, String suffix) {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix(prefix);
        resolver.setSuffix(suffix);
        this.viewResolvers.add(resolver);
        return new UrlBasedViewResolverRegistration(resolver);
    }
}
...//省略代码
```

可以看到上述源码中有两个jsp方法，而没有参数的方法恰恰跟我们配置的内容一样，
这一点看来是Spring早就根据用户使用习惯添加的默认配置，同样也提供了自定义配置Jsp
相关的前缀、后缀内容的方法，方法内部同样是实例化了一个InternalResourceViewResolver 
视图映射类，并将实例添加到了viewResolvers集合内。

>配置MessageConverter

这个配置一般针对于Api接口服务程序，配置在请求返回时内容采用什么转换器进行转换，
我们最常用到的就是fastJson的转换，配置如下所示：

```java
/**
     * 消息内容转换配置
     * 配置fastJson返回json转换
     * @param converters
     */
    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
        //调用父类的配置
        super.configureMessageConverters(converters);
        //创建fastJson消息转换器
        FastJsonHttpMessageConverter fastConverter = new FastJsonHttpMessageConverter();
        //创建配置类
        FastJsonConfig fastJsonConfig = new FastJsonConfig();
        //修改配置返回内容的过滤
        fastJsonConfig.setSerializerFeatures(
                SerializerFeature.DisableCircularReferenceDetect,
                SerializerFeature.WriteMapNullValue,
                SerializerFeature.WriteNullStringAsEmpty
        );
        fastConverter.setFastJsonConfig(fastJsonConfig);
        //将fastjson添加到视图消息转换器列表内
        converters.add(fastConverter);
    }
```


