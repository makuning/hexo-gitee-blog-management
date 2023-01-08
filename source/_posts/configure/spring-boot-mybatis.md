---
title: SpringBoot整合Mybatis
cover: /img/website/cover-article-default.png
comments: true
categories:
  - 程序配置
tags:
  - springboot
  - mybatis
date: 2023-01-04 17:17:49
updated: 2023-01-04 17:17:49
---

# SpringBoot整合Mybatis

## 创建项目

使用Intellij IDEA快速创建SpringBoot项目

![image-20230104172223592](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/configure/spring-boot-mybatis/image-20230104172223592.png)

选择springboot版本号，并且勾选MyBatis与MySQL驱动

![image-20230104183449780](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/configure/spring-boot-mybatis/image-20230104183449780.png)

## 配置MyBatis

SpringBoot项目配置MyBatis特别简单，只需要在配置文件中填写所需的连接信息就行了

在`application.yml`配置文件中添加连接信息

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test
    username: manager
    password: mk123456
```

这样就配置完成了，是不是非常简单

## 创建实体类

创建一个用户装载数据库中的表的数据的实体类

```java
package com.example.main;

import java.util.Date;

/**
 * @author makun
 * @project spring-boot
 * @description 用户实体类
 * @date 2023/01/04 18:45:13
 * version 1.0
 */
public class User {
    private Integer id;
    private String name;
    private String gender;
    private Integer age;
    private String phone;
    private Date birthday;
    private Double balance;

    public User() {
    }

    public User(Integer id, String name, String gender, Integer age, String phone, Date birthday, Double balance) {
        this.id = id;
        this.name = name;
        this.gender = gender;
        this.age = age;
        this.phone = phone;
        this.birthday = birthday;
        this.balance = balance;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", gender='" + gender + '\'' +
                ", age=" + age +
                ", phone='" + phone + '\'' +
                ", birthday=" + birthday +
                ", balance=" + balance +
                '}';
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getPhone() {
        return phone;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }

    public Double getBalance() {
        return balance;
    }

    public void setBalance(Double balance) {
        this.balance = balance;
    }
}

```

## 创建持久层访问类

```java
package com.example.dao;

import com.example.main.User;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;

import java.util.List;

/**
 * @author makun
 * @project spring-boot
 * @description 用户持久层访问
 * @date 2023/01/04 18:55:06
 * version 1.0
 */

// 让Mybatis知道这是一个持久层访问的接口，并且交给SpringBoot管理
@Mapper
public interface UserDao {
    @Select("select * from t_user")
    List<User> queryAll();
}
```

## 创建服务类

创建服务接口

```java
package com.example.service;

import com.example.main.User;
import java.util.List;

/**
 * @author makun
 * @project spring-boot
 * @description 用户服务接口
 * @date 2023/01/04 18:51:37
 * version 1.0
 */

public interface UserService {
    List<User> getAllUsers();
}
```

实现服务类接口

```java
package com.example.service.impl;

import com.example.dao.UserDao;
import com.example.main.User;
import com.example.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.List;

/**
 * @author makun
 * @project spring-boot
 * @description 用户服务类接口实现
 * @date 2023/01/04 18:53:40
 * version 1.0
 */
@Service
public class UserServiceImpl implements UserService {
    @Autowired
    private UserDao userDao;

    @Override
    public List<User> getAllUsers() {
        return userDao.queryAll();
    }
}

```

## 创建持久层访问接口

```java
package com.example.dao;

import com.example.main.User;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;
import java.util.List;

/**
 * @author makun
 * @project spring-boot
 * @description 用户持久层访问
 * @date 2023/01/04 18:55:06
 * version 1.0
 */

// 让Mybatis知道这是一个持久层访问的接口，并且交给SpringBoot管理
@Mapper
public interface UserDao {
    @Select("select * from t_user")
    List<User> queryAll();
}
```

## 编写测试代码

```java
package com.example.service;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

/**
 * @author makun
 * @project spring-boot
 * @description
 * @date 2023/01/04 19:00:17
 * version 1.0
 */
@SpringBootTest
class UserServiceTest {
    @Autowired
    private UserService userService;
    @Test
    void getAllUsers() {
        System.out.println(userService.getAllUsers());
    }
}
```

如果输出正确，表示配置成功

