---
title: SpringMVC配置（一）
cover: /img/website/cover-article-default.png
comments: true
categories:
  - 程序配置
tags:
  - spring
  - mvc
date: 2022-12-11 16:11:04
updated: 2022-12-11 16:11:04
---

# SpringMVC基础配置

## Spring-MVC

之前使用servlet来做表现层非常繁琐，使用spring-mvc能够让表现层的编写更简单

## 创建Spring-MVC项目

### 新建模块

使用IntelliJ IDEA创建一个maven的web项目

![image-20221211162042337](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/configure/spring-mvc/image-20221211162042337.png)

### 导入所需坐标

Spring-MVC需要**spring-webmvc**坐标，我这里使用`5.2.10.RELEASE`版本，导入**sevlet**坐标，我这里使用`3.1.0`，因为这里使用的servlet会和后面的tomcat冲突，所以需要配置作用域

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.10.RELEASE</version>
</dependency>

<!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>
```

### 创建源码目录

创建源码目录并增加controller包与config包

![image-20221211163129855](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/configure/spring-mvc/image-20221211163129855.png)

### 创建控制器

创建org.example.controller.UserController控制器

```java
package org.example.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

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
    // 请求的url地址
    @RequestMapping("/user/save")
    // 让方法返回的结果就作为响应的响应体，不做处理
    @ResponseBody
    public String save() {
        System.out.println("/user/save");
        return "{\"username\":\"John\"}";
    }
}
```

### 创建配置类

创建org.example.config.SpringMVCConfig配置类

```java
package org.example.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

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
public class SpringMVCConfig {
}
```

创建org.example.config.ServletControllerInitConfig配置类

```java
package org.example.config;

import org.springframework.web.context.WebApplicationContext;
import org.springframework.web.context.support.AnnotationConfigWebApplicationContext;
import org.springframework.web.servlet.support.AbstractDispatcherServletInitializer;

/**
 * @author makun
 * @project spring
 * @description servlet初始化配置
 * @date 2022/12/11 16:38:54
 * version 1.0
 */
public class ServletControllerInitConfig extends AbstractDispatcherServletInitializer {
    // 配置Webapp的容器
    protected WebApplicationContext createServletApplicationContext() {
        AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
        // 配置容器的配置类
        context.register(SpringMVCConfig.class);
        return context;
    }

    // 配置哪些请求url交割springmvc处理
    protected String[] getServletMappings() {
        // 表示将所有的请求都交割SpringMVC处理
        return new String[]{"/"};
    }

    // 配置全局的容器
    protected WebApplicationContext createRootApplicationContext() {
        return null;
    }
}
```

### 配置Maven插件

配置maven的tomcat插件

```xml
// 根节点下添加
<build>
    <plugins>
        <!-- 配置tomcat插件 -->
    	<plugin>
        	<groupId>org.apache.tomcat.maven</groupId>
            <artifactId>tomcat7-maven-plugin</artifactId>
            <version>2.1</version>
            <configuration>
            	<port>80</port>
                <path>/</path>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### 运行配置

配置IntelliJ IDEA运行调试配置

![image-20221211165609106](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/configure/spring-mvc/image-20221211165609106.png)

运行访问`http://127.0.0.1:80/user/save`出现以下结果表示成功

![image-20221211172219107](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/configure/spring-mvc/image-20221211172219107.png)
