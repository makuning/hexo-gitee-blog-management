---
title: springboot配置文件
cover: /img/website/cover-article-default.png
comments: true
categories:
  - 程序配置
tags:
  - springboot
  - yaml
date: 2022-12-29 14:11:47
updated: 2022-12-29 14:11:47
---

# SpringBoot配置文件

## 配置文件类型

主要有两种，分别为**properties**格式的和**yaml**格式的

### properties

此配置文件注重格式，不同等级使用`.`分割，如下

```properties
server.port=80
server.xxx.xxx=xxx
server.xxx.xxx.xxx=xxx
```

### yaml

此配置文件后缀分为两种，一种是以`.yml`结尾，较为常用，一种是以`.yaml`结尾，他们的格式都是一样的，只是文件后缀不一样

此配置文件的格式注重数据，以空格` `分级，空格不限次数，建议使用两个空格分级，案例如下

```yaml
student:
  school: "成都工业学院"
  clazz: "软件工程22级专升本1班"
  name: "马某人"
  phone: "19960798888"
  age: 21
  hobbies:
    - "跑步"
    - "骑自行车"
    - "狂吃一顿"
```

## SpringBoot读取YAML配置文件

首先SpringBoot默认会去读取项目根目录下`src/main/resources`目录下的`application.properties`，`application.yml`，`application.yaml`这三个配置文件

并且优先级按从左到右依次降低，SpringBoot读取YAML配置文件分为三种方法

### 使用value注解获取

在类中直接使用`@value`注解来获取，使用`${}`来获取YAML中的数据，案例如下

```java
@Value("${student.hobbies[0]}")
private String hobbit;
```



### 使用Environment对象获取

SpringBoot在容器中已经有了一个装了YAML中所有内容的类，类名为`org.springframework.core.env.Environment`

自动注入即可，案例如下

```java
@Autowired
private Environment environment;

@GetMapping("/environment/student/name")
public JsonData getStudentNameByEnvironment() {
    return new JsonData(0, environment.getProperty("student.name"), "处理成功！");
}
```



### 使用自定义类获取

给需要装载数据的类打上注解，并让Spring容器进行管理

```java
package com.example.domain;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Repository;

import java.util.Arrays;

/**
 * @author makun
 * @project spring-boot
 * @description
 * @date 2022/12/29 13:42:08
 * version 1.0
 */
// 读取YAML中student节点的数据
@ConfigurationProperties(prefix = "student")
// 交给Spring容器管理
@Repository
public class Student {
    private String school;
    private String clazz;
    private String name;
    private String phone;
    private Integer age;
    private String[] hobbies;

    public Student() {
    }

    public Student(String school, String clazz, String name, String phone, Integer age, String[] hobbies) {
        this.school = school;
        this.clazz = clazz;
        this.name = name;
        this.phone = phone;
        this.age = age;
        this.hobbies = hobbies;
    }

    @Override
    public String toString() {
        return "Student{" +
                "school='" + school + '\'' +
                ", clazz='" + clazz + '\'' +
                ", name='" + name + '\'' +
                ", phone='" + phone + '\'' +
                ", age=" + age +
                ", hobbies=" + Arrays.toString(hobbies) +
                '}';
    }

    public String getSchool() {
        return school;
    }

    public void setSchool(String school) {
        this.school = school;
    }

    public String getClazz() {
        return clazz;
    }

    public void setClazz(String clazz) {
        this.clazz = clazz;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPhone() {
        return phone;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String[] getHobbies() {
        return hobbies;
    }

    public void setHobbies(String[] hobbies) {
        this.hobbies = hobbies;
    }
}
```

光完成上面两步还不够，这时候IDEA会有警告，需要在pom配置文件中添加一个依赖坐标，就不会有警告了

```xml
 <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-configuration-processor</artifactId>
     <optional>true</optional>
</dependency>
```

使用方法如下

```java
@Autowired
private Student student;

@GetMapping("/yaml/student")
public JsonData getYamlStudentByDomain() {
    return new JsonData(0, student, "处理成功！");
}
```

