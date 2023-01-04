---
title: Spring配置Mybatis与Junit
cover: /img/website/cover-article-default.png
comments: true
categories:
  - 程序配置
tags:
  - Spring
  - Mybatis
  - Junit
  - IntelliJ IDEA
date: 2022-12-08 19:32:15
updated: 2022-12-08 22:25:15
---

# 在Maven中配置Sping与Mybatis和Junit的整合

## 创建Maven项目

**使用Intellij IDEA快速创建Maven项目**

![image-20221208194747004](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/configure/spring-mybatis-junit/image-20221208194747004.png)

**配置好后会自动加上Junit的坐标，但是版本较老，后面会更换**

测试目录也自动生成了，并且有自带的启动入口

![image-20221208195351476](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/configure/spring-mybatis-junit/image-20221208195351476.png)

## 配置Spring

### 导入Spring坐标

可以到一个[特别方便的网站](https://mvnrepository.com/)来检索你需要的Maven坐标，我们这里先搜索**spring-context**

选择第一个搜索到的结果，我这里选择的是`5.3.18`版本

进去后复制**Maven坐标**，粘贴到项目的pom.xml的dependencies节点下，然后刷新Maven

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.3.18</version>
</dependency>
```

### 创建实体类

创建一个实体类，对应你数据库的表，我数据库中的表格式如下

```mysql
+----+-----------+--------+-----+-------------+------------+
| id | name      | gender | age | phone       | birthday   |
+----+-----------+--------+-----+-------------+------------+
|  1 | 马昆      | male   |  18 | 19960790000 | 2001-06-24 |
|  2 | 马强      | female |  23 | 18328195555 | 1999-09-24 |
|  3 | 罗海人    | female |  22 | 13547682222 | 2000-11-03 |
+----+-----------+--------+-----+-------------+------------+
```

实体类我是创建在org.example.domain包下的，可以先给User类添加上`@component`注解，用于后面测试Spring，代码如下

```java
package org.example.domain;

import java.sql.Date;

/**
 * @author makun
 * @project spring
 * @description User实体类
 * @date 2022/12/08 20:03:13
 * version 1.0
 */

@Component
public class User {
    private Integer id;
    private String name;
    private String gender;
    private Integer age;
    private String phone;
    private Date birthday;

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", gender='" + gender + '\'' +
                ", age=" + age +
                ", phone='" + phone + '\'' +
                ", birthday=" + birthday +
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
}
```

### 创建Spring配置类

在org.example.config包下创建SpringConfig配置类

```java
package org.example.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

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
@ComponentScan({"org.example.domain"})
public class SpringConfig {
}
```

### 测试是否成功

清理App启动类中的代码，并测试Spring是否配置好

```java
package org.example;

import org.example.config.SpringConfig;
import org.example.domain.User;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class App {
    public static void main( String[] args ) {
        // 创建Spring容器的上下文，并传入我们的配置类
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
        // 获取容器中的User
        User user = context.getBean(User.class);
        // 看是否新建成功
        System.out.println(user);
    }
}
```

运行后如果是以下结果表示配置成功

```tex
User{id=null, name='null', gender='null', age=null, phone='null', birthday=null}
```

## 配置阿里Druid数据源

### 导入Druid的坐标

在那个网站搜索**mysql connector java**，我使用的是`5.1.38`版本

在网站中搜索阿里的**Druid**连接池，我使用的是`1.1.10`版本

将这两个坐标加入到pom.xml中

```xml
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.38</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.10</version>
</dependency>
```

### 创建配置文件

在项目的`/src/main/`下创建resource文件夹，并表明为资源文件夹

![image-20221208202635732](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/configure/spring-mybatis-junit/image-20221208202635732.png)

在文件夹下创建`jdbc.properties`配置文件，填写你的数据库连接信息

```properties
# 选择mysql的官方驱动
jdbc.driver=com.mysql.jdbc.Driver
# 后面对应为主机，mysql服务端口，数据库名
jdbc.url=jdbc:mysql://127.0.0.1:3306/test
# 数据库连接用户名
jdbc.username=manager
# 用户密码
jdbc.password=mk123456
```

在org.example.config.SpringConfig中配置jdbc.properties的读取

```java
package org.example.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

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
@ComponentScan({"org.example.domain"})
// 读取配置文件
@PropertySource({"classpath:jdbc.properties"})
public class SpringConfig {
}
```

### 创建JDBC配置类

创建org.example.config.JdbcConfig配置类

```java
package org.example.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;

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
}
```

在org.example.config.SpringConfig中导入jdbc的配置

```java
package org.example.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.PropertySource;

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
@ComponentScan({"org.example.domain"})
// 读取配置文件
@PropertySource({"classpath:jdbc.properties"})
// 导入jdbc配置类
@Import({JdbcConfig.class})
public class SpringConfig {
}
```

### 测试是否成功

在org.example.App中添加一些代码，修改后的代码如下

```java
package org.example;

import org.example.config.SpringConfig;
import org.example.domain.User;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import javax.sql.DataSource;

public class App {
    public static void main( String[] args ) {
        // 创建Spring容器的上下文，并传入我们的配置类
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);
        // 获取容器中的User
        User user = context.getBean(User.class);
        // 看是否新建成功
        System.out.println(user);
        // 获取容器中的DataSource
        DataSource dataSource = context.getBean(DataSource.class);
        System.out.println(dataSource);
    }
}
```

如果运行打印如下结果表示配置成功

```tex
User{id=null, name='null', gender='null', age=null, phone='null', birthday=null}
{
	CreateTime:"2022-12-08 20:49:46",
	ActiveCount:0,
	PoolingCount:0,
	CreateCount:0,
	DestroyCount:0,
	CloseCount:0,
	ConnectCount:0,
	Connections:[
	]
}
```

## 配置Mybatis-Spring

### 导入Mybatis所需坐标

搜索**spring-jdbc**，我使用的是`5.3.18`版本

继续搜索**mybatis**，我使用的是`3.4.6`版本

还需要搜索Mybatis的Spring整合包，在网站搜索**mybatis-spring**，我使用的是`1.3.2`版本

将以上三个坐标添加到pom.xml中

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.18</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.3.2</version>
</dependency>
```

### 创建Mybatis配置类

创建org.example.config.MybatisConfig配置类

```java
package org.example.config;

import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.mapper.MapperScannerConfigurer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;

/**
 * @author makun
 * @project spring
 * @description Mybatis配置类
 * @date 2022/12/08 21:00:19
 * version 1.0
 */
public class MybatisConfig {
    // 在属性中填写的应用类型参数，Spring会帮忙注入
    @Bean
    public SqlSessionFactoryBean sqlSessionFactoryBean(DataSource dataSource) {
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        // 设置数据源
        sqlSessionFactoryBean.setDataSource(dataSource);
        // 设置实体类的包
        sqlSessionFactoryBean.setTypeAliasesPackage("org.example.domain");
        return sqlSessionFactoryBean;
    }

    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer() {
        MapperScannerConfigurer mapperScannerConfigurer = new MapperScannerConfigurer();
        // 设置sql映射的包
        mapperScannerConfigurer.setBasePackage("org.example.dao");
        return mapperScannerConfigurer;
    }
}
```

在SpringConfig配置类中导入Mybatis配置类

```java
package org.example.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.PropertySource;

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
@ComponentScan({"org.example.domain"})
// 读取配置文件
@PropertySource({"classpath:jdbc.properties"})
// 导入jdbc配置类，Mybatis配置类
@Import({JdbcConfig.class})
public class SpringConfig {
}
```



### 创建持久层访问接口

创建org.example.dao.UserDao持久层访问接口

```java
package org.example.dao;

import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;
import org.example.domain.User;
import java.util.List;

/**
 * @author makun
 * @project spring
 * @description
 * @date 2022/12/08 20:06:28
 * version 1.0
 */
public interface UserDao {

    @Insert("insert into t_user(name, gender, age, phone, birthday) values(#{name},#{gender},#{age},#{phone},#{birthday})")
    void save(User account);

    @Delete("delete from t_user where id = #{id} ")
    void deleteById(Integer id);

    @Update("update t_user set name = #{name} , gender = #{gender}, age = #{age}, phone = #{phone}, birthday = #{birthday} where id = #{id} ")
    void update(User user);

    @Select("select * from t_user")
    List<User> findAll();

    @Select("select * from t_user where id = #{id} ")
    User findById(Integer id);
}
```

### 创建服务类

创建org.example.service.UserService服务类接口

```java
package org.example.service;

import org.example.domain.User;
import java.util.List;

/**
 * @author makun
 * @project spring
 * @description User服务类接口
 * @date 2022/12/08 21:11:37
 * version 1.0
 */
public interface UserService {
    void save(User account);

    void deleteById(Integer id);

    void update(User user);

    List<User> findAll();

    User findById(Integer id);
}
```

创建org.example.service.impl.UserServiceImpl服务实现类

```java
package org.example.service.impl;

import org.example.dao.UserDao;
import org.example.domain.User;
import org.example.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
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

    public void save(User account) {
        userDao.save(account);
    }

    public void deleteById(Integer id) {
        userDao.deleteById(id);
    }

    public void update(User user) {
        userDao.update(user);
    }

    public List<User> findAll() {
        return userDao.findAll();
    }

    public User findById(Integer id) {
        return userDao.findById(id);
    }
}
```

设置SpringConfig配置类的组件扫描选项，并删除用户类之前用于测试的`@Component`注解

```java
package org.example.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.PropertySource;

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
@ComponentScan({"org.example.service"})
// 读取配置文件
@PropertySource({"classpath:jdbc.properties"})
// 导入jdbc配置类，Mybatis配置类
@Import({JdbcConfig.class, MybatisConfig.class})
public class SpringConfig {
}
```

### 测试是否成功

重新编写org.example.App主程序

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
    }
}
```

**刚刚踩了一些坑**

1. SpringConfig中没有导入所有的配置，导致有些对象没有加载到容器中
2. JdbcConfig中使用properties中的数据没有对应正确，导致数据库一直连接不上
3. JdbcConfig中DruidDataSource的set没有set正确，导致数据库连接不上

解决以上的问题后，运行程序，能够打印出你数据库中的数据就算成功了，我的输入如下

```tex
十二月 08, 2022 9:41:39 下午 com.alibaba.druid.support.logging.JakartaCommonsLoggingImpl info
信息: {dataSource-1} inited
[User{id=1, name='马昆', gender='male', age=18, phone='19960796404', birthday=2001-06-24}, User{id=2, name='马强', gender='female', age=23, phone='18328195555', birthday=1999-09-24}, User{id=3, name='罗海人', gender='female', age=22, phone='13547682222', birthday=2000-11-03}]
```

## 配置Spring-test与Junit

### 导入测试所需坐标

**先删除之前创建maven项目的时候自动生成的junit坐标**

在网站中搜索**spring-test**与**junit**

我使用的版本分别是`5.3.18`与`4.12`

将坐标导入到pom.xml中

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-test -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.3.18</version>
    <scope>test</scope>
</dependency>

<!-- https://mvnrepository.com/artifact/junit/junit -->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
```

### 配置测试类

重写`/src/test/java/`目录下org.example.AppTest测试类的代码

```java
package org.example;

import org.example.config.SpringConfig;
import org.example.domain.User;
import org.example.service.UserService;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import java.util.List;

// 配置类启动器
@RunWith(SpringJUnit4ClassRunner.class)
// 设置Spring环境对应的配置类
@ContextConfiguration(classes = SpringConfig.class)
public class AppTest {
    @Autowired
    private UserService userService;

    @Test
    public void testFindById() {
        User user = userService.findById(1);
        System.out.println(user);
    }

    @Test
    public void testFindAll() {
        List<User> users = userService.findAll();
        System.out.println(users);
    }
}
```

看是否输出正常，我的输出如下

```tex
十二月 08, 2022 10:07:06 下午 com.alibaba.druid.support.logging.JakartaCommonsLoggingImpl info
信息: {dataSource-1} inited
[User{id=1, name='马昆', gender='male', age=18, phone='19960796404', birthday=2001-06-24}, User{id=2, name='马强', gender='female', age=23, phone='18328195555', birthday=1999-09-24}, User{id=3, name='罗海人', gender='female', age=22, phone='13547682222', birthday=2000-11-03}]
User{id=1, name='马昆', gender='male', age=18, phone='19960796404', birthday=2001-06-24}
```

## 完成后的所有内容

**到此为止就已经完成了配置，项目所有的文件内容列在下边儿**

### main

#### java

##### org.example.config

JdbcConfig

```java
package org.example.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;

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
}

```

MybatisConfig

```java
package org.example.config;

import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.mapper.MapperScannerConfigurer;
import org.springframework.context.annotation.Bean;
import javax.sql.DataSource;

/**
 * @author makun
 * @project spring
 * @description Mybatis配置类
 * @date 2022/12/08 21:00:19
 * version 1.0
 */
public class MybatisConfig {
    // 在属性中填写的应用类型参数，Spring会帮忙注入
    @Bean
    public SqlSessionFactoryBean sqlSessionFactoryBean(DataSource dataSource) {
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        // 设置实体类的包
        sqlSessionFactoryBean.setTypeAliasesPackage("org.example.domain");
        // 设置数据源
        sqlSessionFactoryBean.setDataSource(dataSource);
        return sqlSessionFactoryBean;
    }

    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer() {
        MapperScannerConfigurer mapperScannerConfigurer = new MapperScannerConfigurer();
        // 设置sql映射的包
        mapperScannerConfigurer.setBasePackage("org.example.dao");
        return mapperScannerConfigurer;
    }
}

```

SpringConfig

```java
package org.example.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.PropertySource;

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
@ComponentScan({"org.example.service"})
// 读取配置文件
@PropertySource({"classpath:jdbc.properties"})
// 导入jdbc配置类，Mybatis配置类
@Import({JdbcConfig.class, MybatisConfig.class})
public class SpringConfig {
}

```



##### org.example.dao

UserDao

```java
package org.example.dao;

import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;
import org.example.domain.User;
import java.util.List;

/**
 * @author makun
 * @project spring
 * @description
 * @date 2022/12/08 20:06:28
 * version 1.0
 */
public interface UserDao {

    @Insert("insert into t_user(name, gender, age, phone, birthday) values(#{name},#{gender},#{age},#{phone},#{birthday})")
    void save(User account);

    @Delete("delete from t_user where id = #{id} ")
    void deleteById(Integer id);

    @Update("update t_user set name = #{name} , gender = #{gender}, age = #{age}, phone = #{phone}, birthday = #{birthday} where id = #{id} ")
    void update(User user);

    @Select("select * from t_user")
    List<User> findAll();

    @Select("select * from t_user where id = #{id} ")
    User findById(Integer id);
}

```



##### org.example.domain

User

```java
package org.example.domain;

import java.sql.Date;

/**
 * @author makun
 * @project spring
 * @description User实体类
 * @date 2022/12/08 20:03:13
 * version 1.0
 */
public class User {
    private Integer id;
    private String name;
    private String gender;
    private Integer age;
    private String phone;
    private Date birthday;

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", gender='" + gender + '\'' +
                ", age=" + age +
                ", phone='" + phone + '\'' +
                ", birthday=" + birthday +
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
}

```



##### org.example.service

```java
package org.example.service;

import org.example.domain.User;
import java.util.List;

/**
 * @author makun
 * @project spring
 * @description User服务类接口
 * @date 2022/12/08 21:11:37
 * version 1.0
 */
public interface UserService {
    void save(User account);

    void deleteById(Integer id);

    void update(User user);

    List<User> findAll();

    User findById(Integer id);
}

```

###### org.example.service.impl

UserServiceImpl

```java
package org.example.service.impl;

import org.example.dao.UserDao;
import org.example.domain.User;
import org.example.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

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

    public void save(User account) {
        userDao.save(account);
    }

    public void deleteById(Integer id) {
        userDao.deleteById(id);
    }

    public void update(User user) {
        userDao.update(user);
    }

    public List<User> findAll() {
        return userDao.findAll();
    }

    public User findById(Integer id) {
        return userDao.findById(id);
    }

    /**
     * 构造之后执行
     */
    @PostConstruct
    public void before() {
        System.out.println("UserServiceImpl被创建了");
    }

    /**
     * 被销毁之前执行
     */
    @PreDestroy
    public void after() {
        System.out.println("UserServiceImpl要被销毁了");
    }
}

```



##### org.example.App

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



#### resources

jdbc.properties

```properties
# 选择mysql的官方驱动
jdbc.driver=com.mysql.jdbc.Driver
# 后面对应为主机，mysql服务端口，数据库名
jdbc.url=jdbc:mysql://127.0.0.1:3306/test?useSSL=true
# 数据库连接用户名
jdbc.username=manager
# 用户密码
jdbc.password=mk123456
```



### test

#### java

##### org.example.AppTest

```java
package org.example;

import org.example.config.SpringConfig;
import org.example.domain.User;
import org.example.service.UserService;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import java.util.List;

// 配置类启动器
@RunWith(SpringJUnit4ClassRunner.class)
// 设置Spring环境对应的配置类
@ContextConfiguration(classes = SpringConfig.class)
public class AppTest {
    @Autowired
    private UserService userService;

    @Test
    public void testFindById() {
        User user = userService.findById(1);
        System.out.println(user);
    }

    @Test
    public void testFindAll() {
        List<User> users = userService.findAll();
        System.out.println(users);
    }
}

```

### pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.example</groupId>
  <artifactId>spring-mybatis-junit</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>spring-mybatis-junit</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <!-- Spring框架坐标 -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.3.18</version>
    </dependency>


    <!-- mybatis -->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.4.6</version>
    </dependency>

    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.3.2</version>
    </dependency>


    <!-- JDBC -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.3.18</version>
    </dependency>

    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.38</version>
    </dependency>

    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.10</version>
    </dependency>


    <!-- test -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>5.3.18</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>

```



