---
title: Spring配置AOP
cover: /img/website/cover-article-default.png
comments: true
categories:
  - 程序配置
tags:
  - Spring
  - AOP
date: 2022-12-09 12:37:52
updated: 2022-12-09 12:37:52
---

# Spring配置面向切面编程

## AOP

### 什么是AOP

我也是刚学，就说一下我的个人理解，肯定是有点儿问题的

AOP就是面向切面编程英文的缩写，它的目的就是用来增强已经开发好的功能的，并且不用更改之前的功能代码

AOP的概念中有一些名词，我的解释有些模糊

1. 连接点
   1. 在Spring的AOP中，连接点就是一个类中所有的方法
2. 切入点
   1. 就是你需要增强的方法
3. 通知
   1. 需要增强的功能
4. 切面
   1. 是通知与切入点的关系

### 如何实现的

配置好规则后，Spring会将需要增强的类的bean替换成代理对象，使用的其实是代理类，不是目标对象

## 在Spring中配置AOP

### 导入所需要的坐标

spring-aop在导入spring-context坐标时就已经被包含在其中了，所以不用导此坐标

还需要一个面向切面编程的实现包，导入**aspectjweaver**坐标，我使用的是`1.9.6`版本

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.6</version>
</dependency>
```

### 创建切面类

创建org.example.aop.MyAspect切面类

```java
package org.example.aop;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

/**
 * @author makun
 * @project spring
 * @description 我的切面
 * @date 2022/12/09 12:48:26
 * version 1.0
 */
// 让Spring将通知装入容器中
@Component
// 将类声明为切面，让Spring识别里面与切面相关的的注解
@Aspect
public class MyAspect {
    // 定义切入点，表示匹配org.example.service.impl.UserServiceImpl类中所有为find前缀的public方法，不管形参有多少
    @Pointcut("execution(public * org.example.service.impl.UserServiceImpl.find*(..))")
    private void servicePointCut() {}

    // 定义前置通知，配置设置好的切入点
    @Before("MyAspect.servicePointCut()")
    public void before() {
        System.out.println("我是前置通知");
    }
}
```

### 增加一些配置

在SpringConfig配置类中添加org.example.aop包的扫描，并开启切面自动代理

```java
package org.example.config;

import org.springframework.context.annotation.*;

/**
 * @author makun
 * @project spring
 * @description Spring配置类
 * @date 2022/12/08 20:07:29
 * version 1.0
 */
// 表示这是一个配置类
@Configuration
// 表示需要扫描哪里，将有对应注解的类创建对象并添加到容器中
@ComponentScan({"org.example.service","org.example.aop"})
// 读取配置文件
@PropertySource({"classpath:jdbc.properties"})
// 导入jdbc配置类，Mybatis配置类
@Import({JdbcConfig.class, MybatisConfig.class})
// 开启切面自动代理
@EnableAspectJAutoProxy
public class SpringConfig {
}

```

### 测试是否成功

运行启动类

```java
package org.example;

import org.example.config.SpringConfig;
import org.example.domain.User;
import org.example.service.UserService;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import java.util.List;

public class App {
    public static void main( String[] args ) {
        // 创建Spring容器的上下文，并传入我们的配置类
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
        // 获取容器中的UsrServiceImpl
        UserService service = context.getBean(UserService.class);
        // 查找所有用户
        List<User> users = service.findAll();
        // 打印所有用户
        System.out.println(users);
        // 关闭钩子
        context.registerShutdownHook();
        /*
        * 关闭
        * context.close();
        * */
    }
}
```

如果执行了前置通知方法表示配置正确，以下是我的输出

```tex
UserServiceImpl被创建了
我是前置通知
十二月 09, 2022 1:14:44 下午 com.alibaba.druid.support.logging.JakartaCommonsLoggingImpl info
信息: {dataSource-1} inited
[User{id=1, name='马昆', gender='male', age=18, phone='19960796404', birthday=2001-06-24}, User{id=2, name='马强', gender='female', age=23, phone='18328195555', birthday=1999-09-24}, User{id=3, name='罗海人', gender='female', age=22, phone='13547682222', birthday=2000-11-03}]
UserServiceImpl要被销毁了
```

## 常用的一些通知

所有案例

```java
package org.example.aop;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

/**
 * @author makun
 * @project spring
 * @description 我的切面
 * @date 2022/12/09 12:48:26
 * version 1.0
 */
// 让Spring将通知装入容器中
@Component
// 将类声明为切面，让Spring识别里面与切面相关的的注解
@Aspect
public class MyAspect {
    // 定义切入点匹配规则，表示匹配org.example.service.impl.UserServiceImpl类中所有为find前缀的public方法，不管形参有多少
    @Pointcut("execution(public * org.example.service.impl.UserServiceImpl.find*(..))")
    private void servicePointCut() {}

    // 定义切入点匹配规则，用来测试我的后置异常通知
    @Pointcut("execution(public void org.example.service.impl.UserServiceImpl.testException())")
    private void serviceExceptionPointCut() {}

    // 定义前置通知，配置设置好的切入点匹配规则
    @Before("MyAspect.servicePointCut()")
    public void before() {
        System.out.println("我是前置通知");
    }

    // 定义后置通知
    @After("MyAspect.servicePointCut()")
    public void after() {
        System.out.println("我是后置通知");
    }

    // 定义环绕通知
    @Around("MyAspect.servicePointCut()")
    public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        // 用于获取切入点方法的签名
        Signature signature = proceedingJoinPoint.getSignature();
        // 获取方法的类名
        String declaringTypeName = signature.getDeclaringTypeName();
        // 获取方法名
        String name = signature.getName();
        long start = System.currentTimeMillis();
        // 使用此方法执行切入点的方法
        Object result = proceedingJoinPoint.proceed();
        long end = System.currentTimeMillis();
        // 打印方法执行花费时间
        System.out.println(declaringTypeName + "." + name + "使用了" + (end - start) + "ms");
        return result;
    }

    // 定义后置返回通知，将返回结果与形参绑定，如果使用JoinPoint，那么一定要放在第一个形参
    @AfterReturning(value = "MyAspect.servicePointCut()", returning = "result")
    public Object afterReturn(JoinPoint joinPoint, Object result) {
        Signature signature = joinPoint.getSignature();
        System.out.println("这是" + signature.getName() + "方法");
        System.out.println(result);
        return result;
    }

    // 定义后置返回通知，将异常结果与形参绑定，如果使用JoinPoint，那么还是需要放在形参的第一个位置
    @AfterThrowing(value = "MyAspect.serviceExceptionPointCut()", throwing = "throwable")
    public void afterException(JoinPoint joinPoint, Throwable throwable) {
        System.out.println(joinPoint.getSignature().getName() + "发生了" + throwable.getMessage() + "异常");
    }
}

```



### 前置通知

用于切入点方法执行前

```java
// 定义前置通知，配置设置好的切入点匹配规则
@Before("MyAspect.servicePointCut()")
public void before() {
    System.out.println("我是前置通知");
}
```



### 后置通知

用于切入点方法执行后

```java
// 定义后置通知
@After("MyAspect.servicePointCut()")
public void after() {
    System.out.println("我是后置通知");
}
```



### 环绕通知

**这个通知非常常用**

用于包裹住切入点方法的执行

```java
// 定义环绕通知
@Around("MyAspect.servicePointCut()")
public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
    // 用于获取切入点方法的签名
    Signature signature = proceedingJoinPoint.getSignature();
    // 获取方法的类名
    String declaringTypeName = signature.getDeclaringTypeName();
    // 获取方法名
    String name = signature.getName();
    long start = System.currentTimeMillis();
    // 使用此方法执行切入点的方法
    Object result = proceedingJoinPoint.proceed();
    long end = System.currentTimeMillis();
    // 打印方法执行花费时间
    System.out.println(declaringTypeName + "." + name + "使用了" + (end - start) + "ms");
    return result;
}
```



### 后置返回通知

用于方法执行完成后

```java
// 定义后置返回通知，将返回结果与形参绑定，如果使用JoinPoint，那么一定要放在第一个形参
@AfterReturning(value = "MyAspect.servicePointCut()", returning = "result")
public Object afterReturn(JoinPoint joinPoint, Object result) {
    Signature signature = joinPoint.getSignature();
    System.out.println("这是" + signature.getName() + "方法");
    System.out.println(result);
    return result;
}
```



### 后置异常通知

用于方法抛出异常后

```java
// 定义后置返回通知，将异常结果与形参绑定，如果使用JoinPoint，那么还是需要放在形参的第一个位置
@AfterThrowing(value = "MyAspect.serviceExceptionPointCut()", throwing = "throwable")
public void afterException(JoinPoint joinPoint, Throwable throwable) {
    System.out.println(joinPoint.getSignature().getName() + "发生了" + throwable.getMessage() + "异常");
}
```

需要定义一个会抛异常的切入点

```java
public void testException() {
    int i = 1 / 0;
}
```

### 执行结果

配置上述所有的通知后，执行启动类代码

```java
package org.example;

import org.example.config.SpringConfig;
import org.example.domain.User;
import org.example.service.UserService;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import java.util.List;

public class App {
    public static void main( String[] args ) {
        // 创建Spring容器的上下文，并传入我们的配置类
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
        // 获取容器中的UsrServiceImpl
        UserService service = context.getBean(UserService.class);
        // 查找所有用户
        List<User> users = service.findAll();
        // 测试后置异常通知
        service.testException();
        // 关闭钩子
        context.registerShutdownHook();
        /*
        * 关闭
        * context.close();
        * */
    }
}
```

执行结果如下

```tex
UserServiceImpl被创建了
我是前置通知
十二月 09, 2022 1:47:51 下午 com.alibaba.druid.support.logging.JakartaCommonsLoggingImpl info
信息: {dataSource-1} inited
这是findAll方法
[User{id=1, name='马昆', gender='male', age=18, phone='19960796404', birthday=2001-06-24}, User{id=2, name='马强', gender='female', age=23, phone='18328195555', birthday=1999-09-24}, User{id=3, name='罗海人', gender='female', age=22, phone='13547682222', birthday=2000-11-03}]
我是后置通知
org.example.service.UserService.findAll使用了222ms
testException发生了/ by zero异常
Exception in thread "main" java.lang.ArithmeticException: / by zero
	at org.example.service.impl.UserServiceImpl.testException(UserServiceImpl.java:47)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:344)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.invokeJoinpoint(ReflectiveMethodInvocation.java:198)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:163)
	at org.springframework.aop.aspectj.AspectJAfterThrowingAdvice.invoke(AspectJAfterThrowingAdvice.java:64)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186)
	at org.springframework.aop.interceptor.ExposeInvocationInterceptor.invoke(ExposeInvocationInterceptor.java:97)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186)
	at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:215)
	at com.sun.proxy.$Proxy37.testException(Unknown Source)
	at org.example.App.main(App.java:18)

进程已结束,退出代码1
```

## 切入点的匹配规则

1. `*`表示单位内随意
2. `..`表示任意个随意

```java
// 定义切入点匹配规则，表示匹配org.example.service.impl.UserServiceImpl类中所有为find前缀的public方法，不管形参有多少
@Pointcut("execution(public * org.example.service.impl.UserServiceImpl.find*(..))")
private void servicePointCut() {}

// 表示匹配所有为find开头的方法
@Pointcut("execution(* * ..find*(..))")
private void servicePointCut() {}
```



