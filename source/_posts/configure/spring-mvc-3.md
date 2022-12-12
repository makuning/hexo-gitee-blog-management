---
title: SpringMVC配置（三）
cover: /img/website/cover-article-default.png
comments: true
categories:
  - 程序配置
tags:
  - spring
  - mvc
date: 2022-12-12 15:03:25
updated: 2022-12-12 15:03:25
---

# Spring-MVC入门配置

## REST风格

### REST介绍

一种比较好的url风格

没有使用REST

- /user/findById?id=1		查找用户
- /user/deleteById?id=1      删除用户

使用了REST

- /user/1	 	get		查找用户

- /user/1         delete     删除用户
- /user           post       保存用户
- /user           put        更新用户

也是使用url+方法的方式来区分url的功能

### SpringMVC中的实现

```java
package org.example.controller;

import org.example.domain.JsonResult;
import org.example.domain.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

/**
 * @author makun
 * @project spring
 * @description 用户控制器
 * @date 2022/12/11 16:32:22
 * version 1.0
 */
// 让Spring容器进行管理
@Controller
public class UserController {
    // 请求的url
    // 设置请求的方法，使用REST风格
    @RequestMapping(value = "/user", method = RequestMethod.POST)
    // 让方法返回的结果就作为响应的响应体，不做处理
    @ResponseBody
    public JsonResult save(@RequestBody User user) {
        return new JsonResult(200, "保存成功！", user);
    }

    @RequestMapping(value = "/user", method = RequestMethod.PUT)
    @ResponseBody
    public JsonResult update(@RequestBody User user) {
        return new JsonResult(200, "更新成功！", user);
    }

    @RequestMapping(value = "/user/{id}", method = RequestMethod.DELETE)
    @ResponseBody
    public JsonResult deleteById(@PathVariable Integer id) {
        return new JsonResult(200, id + "删除成功！", null);
    }

    @RequestMapping(value = "/user/{id}", method = RequestMethod.GET)
    @ResponseBody
    public JsonResult findById(@PathVariable Integer id) {
        return new JsonResult(200, id + "查找成功！", new User());
    }
}
```

### 简化

通过上面的代码发现有很多重复的地方，写起来非常繁琐，可以简化为如下

```java
package org.example.controller;

import org.example.domain.JsonResult;
import org.example.domain.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

/**
 * @author makun
 * @project spring
 * @description 用户控制器
 * @date 2022/12/11 16:32:22
 * version 1.0
 */
// 让Spring容器进行管理
@Controller
// 表示所有的方法都将返回结果作为响应体
@ResponseBody
// 将url公共部分提取出来，即url前缀
@RequestMapping("/user")
public class UserController {
    @PostMapping
    public JsonResult save(@RequestBody User user) {
        return new JsonResult(200, "保存成功！", user);
    }

    @PutMapping
    public JsonResult update(@RequestBody User user) {
        return new JsonResult(200, "更新成功！", user);
    }

    @DeleteMapping("/{id}")
    public JsonResult deleteById(@PathVariable Integer id) {
        return new JsonResult(200, id + "删除成功！", null);
    }

    @GetMapping("/{id}")
    @ResponseBody
    public JsonResult findById(@PathVariable Integer id) {
        return new JsonResult(200, id + "查找成功！", new User());
    }
}
```

其中`@Controller`注解可以与`@ResponseBody`注解融合成一个注解，即`@RestController`，最终简化如下

```java
package org.example.controller;

import org.example.domain.JsonResult;
import org.example.domain.User;
import org.springframework.web.bind.annotation.*;

/**
 * @author makun
 * @project spring
 * @description 用户控制器
 * @date 2022/12/11 16:32:22
 * version 1.0
 */
// 表示所有的方法都将返回结果作为响应体，并让Spring容器进行管理
@RestController
// 将url公共部分提取出来，即url前缀
@RequestMapping("/user")
public class UserController {
    @PostMapping
    public JsonResult save(@RequestBody User user) {
        return new JsonResult(200, "保存成功！", user);
    }

    @PutMapping
    public JsonResult update(@RequestBody User user) {
        return new JsonResult(200, "更新成功！", user);
    }

    @DeleteMapping("/{id}")
    public JsonResult deleteById(@PathVariable Integer id) {
        return new JsonResult(200, id + "删除成功！", null);
    }

    @GetMapping("/{id}")
    @ResponseBody
    public JsonResult findById(@PathVariable Integer id) {
        return new JsonResult(200, id + "查找成功！", new User());
    }
}
```

## 异常处理

### 简介

程序中会出现一系列的异常，我们不能保证能够捕获所有的异常，可以使用spring的aop为表现层的方法设置异常捕获切面

### 创建异常通知

创建org.example.controller.aop.ProjectExceptionAdvice异常通知类

```java
package org.example.controller.aop;

import org.example.domain.JsonResult;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

/**
 * @author makun
 * @project spring
 * @description 异常处理
 * @date 2022/12/12 15:35:54
 * version 1.0
 */

// 让springmvc将此类处理为异常通知，并将处理方法的返回值作为响应体
@RestControllerAdvice
public class ProjectExceptionAdvice {
    // 处理所有异常
    @ExceptionHandler(Exception.class)
    public JsonResult exceptionHandler(Exception exception) {
        // 表示处理所有的异常，并返回一个对象消息
        return new JsonResult(500, exception.getMessage(), null);
    }
}
```

重启服务将会开启全局异常处理

### 细分异常

通常我们会对不同层面的异常有不同的处理方案

例如如果因为用户的输入不正确导致的表现层异常那么我们就只需要提醒用户输入正确就行了

但是如果是系统异常，那么我们需要记录日志并且通知运维人员及时修复

如果是未知的系统异常，那么我们也需要记录日志并且通知程序员进行修复，如果不是程序员的错误，程序员只需要将此错误重新归类到系统异常或者业务异常就行了

#### 创建业务异常

创建org.example.exception.BusinessException

```java
package org.example.exception;

/**
 * @author makun
 * @project spring
 * @description 业务异常
 * @date 2022/12/12 15:47:56
 * version 1.0
 */
public class BusinessException extends RuntimeException {
    private Integer code;

    public BusinessException() {
    }

    public BusinessException(Integer code) {
        this.code = code;
    }

    public BusinessException(Integer code, String message) {
        super(message);
        this.code = code;
    }

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }
}
```



#### 创建系统异常

创建org.example.exception.SystemException

```java
package org.example.exception;

/**
 * @author makun
 * @project spring
 * @description 系统异常
 * @date 2022/12/12 15:47:22
 * version 1.0
 */
public class SystemException extends RuntimeException {
    private Integer code;

    public SystemException() {
    }

    public SystemException(Integer code) {
        this.code = code;
    }

    public SystemException(Integer code, String message) {
        super(message);
        this.code = code;
    }

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }
}
```

#### 添加异常处理

```java
package org.example.controller.aop;

import org.example.domain.JsonResult;
import org.example.exception.BusinessException;
import org.example.exception.SystemException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

/**
 * @author makun
 * @project spring
 * @description 异常处理
 * @date 2022/12/12 15:35:54
 * version 1.0
 */

// 让springmvc将此类处理为异常通知，并将处理方法的返回值作为响应体
@RestControllerAdvice
public class ProjectExceptionAdvice {
    // 处理系统异常
    @ExceptionHandler(SystemException.class)
    public JsonResult businessException(SystemException systemException) {
        // 记录日志

        // 给运维发送邮件（消息）

        return new JsonResult(500, "系统繁忙，请稍后再试", null);
    }

    // 处理业务异常
    @ExceptionHandler(BusinessException.class)
    public JsonResult businessException(BusinessException businessException) {
        return new JsonResult(400, "请不要使用违规操作", null);
    }

    // 处理未知异常
    @ExceptionHandler(Exception.class)
    public JsonResult exceptionHandler(Exception exception) {
        // 记录日志

        // 给程序员发送邮件（消息）

        // 返回一个对象消息
        return new JsonResult(500, exception.getMessage(), null);
    }
}
```

#### 测试

写一个有异常的controller处理方法来进行测试

```java
@GetMapping("/test/exception/{id}")
public JsonResult testException(@PathVariable Integer id) {
    if (id == 1) {
        throw new BusinessException();
    } else {
        throw new SystemException();
    }
}
```

访问`http://127.0.0.1/user/test/exception/1`，出现以下结果

```json
{
    "code": 400,
    "message": "请不要使用违规操作",
    "data": null
}
```

访问`http://127.0.0.1/user/test/exception/2`，出现以下结果

```json
{
    "code": 500,
    "message": "系统繁忙，请稍后再试",
    "data": null
}
```

## 开放静态资源

### 创建支持类

创建org.example.config.SpringMvcSupport支持类

```java
package org.example.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;

/**
 * @author makun
 * @project spring
 * @description 支持类
 * @date 2022/12/12 16:09:08
 * version 1.0
 */
@Configuration
public class SpringMvcSupport extends WebMvcConfigurationSupport {
    // 增加资源处理器
    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        // 表示将webapp目录下的pages目录下的所有文件都映射在/pages/**这个url地址中
        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
    }
}
```

### 载入容器中

在org.example.config.SpringMvcConfig中添加扫描

```java
package org.example.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;

/**
 * @author makun
 * @project spring
 * @description mvc配置类
 * @date 2022/12/11 16:35:55
 * version 1.0
 */
// 让此类以配置类的角色交给Spring容器管理
@Configuration
// 配置控制器扫描
@ComponentScan({"org.example.controller", "org.example.config"})
// 开启功能
@EnableWebMvc
// 载入支持配置
public class SpringMvcConfig {
}
```

在webapp文件夹下创建pages文件夹，在pages文件夹下创建一个index.html文件

访问`http://127.0.0.1/pages/index.html`即可获取到页面

### 简化代码

不需要创建名为org.example.config.SpringMvcSupport的支持类，直接在org.example.config.SpringMvcConfig中进行支持的编写

删除org.example.config.SpringMvcSupport类，并修改org.example.config.SpringMvcConfig配置类

```java
package org.example.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * @author makun
 * @project spring
 * @description mvc配置类
 * @date 2022/12/11 16:35:55
 * version 1.0
 */
// 让此类以配置类的角色交给Spring容器管理
@Configuration
// 配置控制器扫描
@ComponentScan({"org.example.controller"})
// 开启功能
@EnableWebMvc
// 载入支持配置
public class SpringMvcConfig implements WebMvcConfigurer {
    // 增加资源处理器
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        // 表示将webapp目录下的pages目录下的所有文件都映射在/pages/**这个url地址中
        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
    }
}
```

访问`http://127.0.0.1/pages/index.html`依然可行

## 拦截器

### 简介

拦截器与过滤器有很大的区别，过滤器是servlet的技术，范围较大

而拦截器是SpringMVC的技术，范围较小，而且与SpringMVC配合起来更加灵活

### 创建拦截器

创建org.example.controller.interceptor.ProjectInterceptor拦截器类

```java
package org.example.controller.interceptor;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * @author makun
 * @project spring
 * @description 拦截器
 * @date 2022/12/12 16:05:02
 * version 1.0
 */

// 交给SpringMVC容器管理
@Component
public class ProjectInterceptor implements HandlerInterceptor {
    // 运行在控制器处理方法之前
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle");
        return true;
    }

    // 运行在控制器处理方法之后
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle");
    }

    // 运行在控制器处理方法完成之后
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion");
    }
}
```

### 更新Spring配置

因为之前使用了支持的简化方法，所以在SpringMvcConfig配置类中添加拦截器支持

```java
package org.example.config;

import org.example.controller.interceptor.ProjectInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * @author makun
 * @project spring
 * @description mvc配置类
 * @date 2022/12/11 16:35:55
 * version 1.0
 */
// 让此类以配置类的角色交给Spring容器管理
@Configuration
// 配置控制器扫描
@ComponentScan({"org.example.controller"})
// 开启功能
@EnableWebMvc
// 载入支持配置
public class SpringMvcConfig implements WebMvcConfigurer {
    // 自动注入容器中我们之前写的拦截器
    @Autowired
    private ProjectInterceptor projectInterceptor;

    public void addInterceptors(InterceptorRegistry registry) {
        // 表示匹配/user路径与/user/下所有的路径
        // 被匹配的路径就会按照编写的拦截器进行拦截
        registry.addInterceptor(projectInterceptor).addPathPatterns("/user","/user/**");
    }

    // 增加资源处理器
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        // 表示将webapp目录下的pages目录下的所有文件都映射在/pages/**这个url地址中
        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
    }
}
```

配置完成，访问`http://127.0.0.1/user`出现以下打印表示配置正确

```tex
preHandle
postHandle
afterCompletion
```

### 多个拦截器配置

多个拦截器也是一样的步骤，先创建一个新的拦截器，然后再在Spring配置类中添加就行了，如：

```java
    // 自动注入容器中我们之前写的拦截器
    @Autowired
    private ProjectInterceptor projectInterceptor;
	// 创建的第二个拦截器
	@Autowired
    private ProjectInterceptor2 projectInterceptor2;

    public void addInterceptors(InterceptorRegistry registry) {
        // 表示匹配/user路径与/user/下所有的路径
        // 被匹配的路径就会按照编写的拦截器进行拦截
        registry.addInterceptor(projectInterceptor).addPathPatterns("/user","/user/**");
        // 添加第二个拦截器规则，会形成包裹
        registry.addInterceptor(projectInterceptor2).addPathPatterns("/user","/user/**");
    }
```

### 拦截器中的参数

目前只列出了以下内容，还有更多的需要自己去探索

```java
package org.example.controller.interceptor;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.example.domain.JsonResult;
import org.example.domain.User;
import org.springframework.stereotype.Component;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.PrintWriter;

/**
 * @author makun
 * @project spring
 * @description 拦截器
 * @date 2022/12/12 16:05:02
 * version 1.0
 */

// 交给SpringMVC容器管理
@Component
public class ProjectInterceptor implements HandlerInterceptor {
    // 运行在控制器处理方法之前
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 获取请求头Content-Type的属性值
        String header = request.getHeader("Content-Type");
        System.out.println(header);

        // 获取控制器对应的那个处理方法，也就是controller中的那个方法
        HandlerMethod handlerMethod = (HandlerMethod) handler;
        // 打印对应controller的类型
        System.out.println(handlerMethod.getBeanType());
        // 执行对应controller中对应的方法
        handlerMethod.getMethod().invoke(handlerMethod.getBean(), new User());

        if (request.getRequestURI().equals("/user")) {
            response.setHeader("Content-Type", "application/json");
            // 获取写入响应体数据的流
            PrintWriter writer = response.getWriter();
            // 写入经jackson对象处理后的JsonResult的json字符串
            writer.write(new ObjectMapper().writeValueAsString(new JsonResult(200, "please login first", null)));
            // 刷新输出流
            writer.flush();
            // 关闭输出流
            writer.close();
            // 不再往后执行
            return false;
        }
        
        // 放行
        return true;
    }

    // 运行在控制器处理方法之后
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle");
    }

    // 运行在控制器处理方法完成之后
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion");
    }
}
```

