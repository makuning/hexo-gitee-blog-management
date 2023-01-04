---
title: 使用IDEA快速创建Springboot项目
cover: /img/website/cover-article-default.png
comments: true
categories:
  - 程序配置
tags:
  - spring
  - springboot
date: 2022-12-19 15:22:01
updated: 2022-12-19 15:22:01
---

# 快速创建SpringBoot项目

## 简介

SpringBoot是Spring家族特别重要的一员，能够大大简化spring的配置。

[SpringBoot官网](https://spring.io/projects/spring-boot)

## 快速创建SpringBoot项目

进入快速创建SpringBoot项目的[官网](https://start.spring.io/)

配置好对应的选项

![image-20221219152851678](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/configure/spring-boot/image-20221219152851678.png)

点击右侧的`ADD DEPENDENCIES`选项，添加`spring web`依赖包

![image-20221219153025959](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/configure/spring-boot/image-20221219153025959.png)

点击`GENERATE`按钮，会让你下载一个zip包，这就是我们的springboot项目

## 使用IntelliJ IDEA创建SpringBoot项目

当然，IDEA也为我们提供了快速创建Springboot项目的方法，用的就是上面Spring官网的接口

![image-20221219160208489](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/configure/spring-boot/image-20221219160208489.png)

选择web，点击创建

创建UserController类

```java
package com.example.controller;

import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author makun
 * @project spring-boot
 * @description 用户控制类
 * @date 2022/12/19 17:34:48
 * version 1.0
 */
@RestController
@RequestMapping("/user")
public class UserController {
    @RequestMapping("/{id}")
    public String getUserById(@PathVariable int id) {
        System.out.println("id ---> " + id);
        return "hello Spring boot";
    }
}

```

点击启动，控制台出现以下内容，表示启动成功

![image-20221219173925854](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/configure/spring-boot/image-20221219173925854.png)

访问`http://127.0.0.1:8080/user/1`，如果显示**hello Spring boot**，表示访问成功
