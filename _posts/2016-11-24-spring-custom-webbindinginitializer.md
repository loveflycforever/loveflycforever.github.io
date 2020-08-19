---
layout: post
title:  "定义自己的WebBindingInitializer"
tags: [Spring]
comments: true
---
1.定义自己的WebBindingInitializer

```java
public class MyWebBindingInitializer implements WebBindingInitializer {  
    @Override  
    public void initBinder(WebDataBinder binder, WebRequest request) {  
        binder.registerCustomEditor(Date.class, 
                                    new CustomDateEditor(
                                        new SimpleDateFormat("yyyy-MM-dd"), 
                                        false));  
    }  
}  
```

2.在springMVC的配置文件里面定义一个AnnotationMethodHandlerAdapter，并设置其WebBindingInitializer属性为我们自己定义的WebBindingInitializer对象

```
<bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">  
    <property name="cacheSeconds" value="0"/>  
    <property name="webBindingInitializer">  
        <bean class="io.github.superfan.util.MyWebBindingInitializer"/>  
    </property>  
</bean>  
```

**注意**

当使用了<mvc:annotation-driven />的时候，它会自动注册DefaultAnnotationHandlerMapping与AnnotationMethodHandlerAdapter两个bean。

这时候指定的全局属性编辑器就不会起作用了，解决办法就是手动的添加上述bean，并把它们加在<mvc:annotation-driven/>的前面。

如果不生效，则将手动注册AnnotationMethodHandlerAdapter改为手动注册RequestMappingHandlerAdapter。
