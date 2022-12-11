---
title: spring事务配置
cover: /img/website/cover-article-default.png
comments: true
categories:
  - 程序配置
tags:
  - spring
  - transaction
  - 事务
date: 2022-12-11 14:01:20
updated: 2022-12-11 14:01:20
---

# Spring事务控制

## 事务

### 什么是事务

在处理一件事情的时候，可能会涉及多个需要与数据库交互的操作，如果这件事中途出现问题，那么所有修改数据库的操作都应该撤回

这一件事情就是事务，我们希望处理一件事时，修改数据库的操作要么一起成功或者一起失败（也有可能部分一定要成功，如日志功能）

## 配置事务管理

### 说明

我们使用的DataSource是阿里云的Druid，使用的数据层访问框架是Mybatis，他们底层的事务控制都是JDBC，刚好Spring自带的事务控制也是使用的JDBC，所以不用导入新的坐标，直接往Spring容器中添加一个事务控制的实现类，然后使用它就行了。

### 创建事务管理的Bean

在org.example.config.JdbcConfig配置类中添加一个叫做**PlatformTransactionManager**平台事务管理器的Bean

```java
package org.example.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.PlatformTransactionManager;

import javax.sql.DataSource;

/**
 * @author makun
 * @project spring
 * @description jdbc配置类
 * @date 2022/12/08 20:43:30
 * version 1.0
 */
public class JdbcConfig {
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;

    @Bean
    public DruidDataSource druidDataSource() {
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setDriverClassName(driver);
        druidDataSource.setUrl(url);
        druidDataSource.setUsername(username);
        druidDataSource.setPassword(password);
        return druidDataSource;
    }

    @Bean
    public PlatformTransactionManager platformTransactionManager(DataSource dataSource) {
        // 创建它的实现类
        DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
        // 为它注入容器中的Datasource
        dataSourceTransactionManager.setDataSource(dataSource);
        return dataSourceTransactionManager;
    }
}
```

### 配置事务

指定哪些服务是需要事务的，将注释加在接口上，实现类上都可以，方法上也行

```java
package org.example.service.impl;

import org.example.dao.UserDao;
import org.example.domain.User;
import org.example.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
import java.util.List;

/**
 * @author makun
 * @project spring
 * @description User服务实现类
 * @date 2022/12/08 21:13:35
 * version 1.0
 */
@Service
public class UserServiceImpl implements UserService {
    // 自动装配
    @Autowired
    private UserDao userDao;

    // 事务测试
    @Transactional
    public void transfer(Integer reference, Integer dest, Double money) {
        User user1 = userDao.findById(reference);
        user1.setBalance(user1.getBalance() - money);
        // 减钱
        userDao.update(user1);
        
        // 创建一个异常
        int i = 1 / 0;

        User user2 = userDao.findById(dest);
        user2.setBalance(user2.getBalance() + money);
        // 加钱
        userDao.update(user2);
    }
}
```

### 开启事务注解

在org.example.config.SpringConfig配置类中开启事务注解

```java
package org.example.config;

import org.springframework.context.annotation.*;
import org.springframework.transaction.annotation.EnableTransactionManagement;

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
// 开启事务注解
@EnableTransactionManagement
public class SpringConfig {
}
```

### 测试是否成功

编写测试类

```java
package org.example.service.impl;

import org.example.config.SpringConfig;
import org.example.service.UserService;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

/**
 * @author makun
 * @project spring
 * @description
 * @date 2022/12/11 14:42:18
 * version 1.0
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = {SpringConfig.class})
public class UserServiceImplTest {
    @Autowired
    private UserService userService;

    @Test
    public void testTransfer() {
        userService.transfer(1,2,50D);
    }
}
```

如果运行后，数据库中数据没有发生变换表示配置成功

## 事务扩展使用

### 事务角色

1. 事务管理员：发起事务方
2. 事务协调员：加入事务方

事务有很多属性，可以百度一下，新手入门
