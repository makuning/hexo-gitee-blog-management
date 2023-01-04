---
title: SpringBoot多环境配置文件
cover: /img/website/cover-article-default.png
comments: true
categories:
  - 程序配置
tags:
  - springboot
  - yaml
  - profile
date: 2023-01-04 11:06:40
updated: 2023-01-04 11:06:40
---

# SpringBoot多环境配置文件方案

## yaml

在`/src/main/resources/`下创建配置文件`application.yml`，写入以下内容

```yaml
# 设置启用的环境
spring:
  profiles:
    active: dev

---
# 开发环境
spring:
  profiles: dev
server:
  port: 80

---
# 生产
spring:
  profiles: pro
server:
  port: 81

---
# 测试
spring:
  profiles: test
server:
  port: 82
```

如果使用`spring.profiles`的话会提示已过时，可以使用`spring.config.activate.on-profile`来代替

```yaml
# 设置启用的环境
spring:
  profiles:
    active: dev

---
# 开发环境
spring:
  config:
    activate:
      on-profile: dev
server:
  port: 80

---
# 生产
spring:
  config:
    activate:
      on-profile: pro
server:
  port: 81

---
# 测试
spring:
  config:
    activate:
      on-profile: test
server:
  port: 82
```



## profile

在`/src/main/resources/`下创建配置文件`application.properties`，写入以下内容

```properties
# 设置启用的环境
spring.profiles.active=dev
```

如果在`application.properties`配置的是dev，那么就会在`/src/main/resources/`下找`application-dev.properties`这个配置文件

创建`application-dev.properties`配置文件，写入以下内容

```properties
server.port=8080
```

如果有其他环境，创建对应的`application-xxx.properties`配置文件，然后再在`application.properties`主配置文件中配置就行了

## 环境切换

如果每次更改环境就将配置文件更改一次然后重新编译项目，这是非常麻烦的

使用maven打包为jar包后，只需要在运行命令中添加参数就能够更改项目的运行环境

```bash
$ java -jar xxx.jar --spring.profiles.active=test
```

并且不是只能使用一个参数，可以使用很多个参数，这样就可以修改很多的值

```bash
$ java -jar xxx.jar --spring.profiles.active=test --server.port=88
```

## maven环境

当然，maven也有多环境配置，并且maven的配置优先级还高一些，因为jar包是由maven来打包的

在`pom.xml`中的`build`节点和`profiles`节点配置以下内容

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.7</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>spring-boot-test-03</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>spring-boot-test-03</name>
    <description>spring-boot-test-03</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!-- springboot打包插件 -->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <!-- 将maven中的变量引用到配置文件中的插件 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>3.2.0</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                    <!-- 使用默认分隔符，不太清楚什么意思 -->
                    <useDefaultDelimiters>true</useDefaultDelimiters>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <profiles>
        <!-- 开发环境 -->
        <profile>
            <id>dev</id>
            <!-- 设置配置变量 -->
            <properties>
                <profile.active>test</profile.active>
            </properties>
            <activation>
                <!-- 设置为默认配置 -->
                <activeByDefault>true</activeByDefault>
            </activation>
        </profile>

        <!-- 生产环境 -->
        <profile>
            <id>pro</id>
            <!-- 设置配置变量 -->
            <properties>
                <profile.active>pro</profile.active>
            </properties>
        </profile>

        <!-- 测试环境 -->
        <profile>
            <id>test</id>
            <!-- 设置配置变量 -->
            <properties>
                <profile.active>test</profile.active>
            </properties>
        </profile>
    </profiles>
</project>

```

`pom.xml`配置好后，还需要在yaml中配置我们定义好的变量，使用`${}`即可引用

```yaml
# 设置启用的环境
spring:
  profiles:
    active: ${profile.active}

---
# 开发环境
spring:
  config:
    activate:
      on-profile: dev
server:
  port: 80

---
# 生产
spring:
  config:
    activate:
      on-profile: pro
server:
  port: 81

---
# 测试
spring:
  config:
    activate:
      on-profile: test
server:
  port: 82
```

## 配置文件优先级

具体优先级可到官网进行查看

以下列出我目前已知的优先级（自上而下，优先级由高到低）

1. 运行打包好的jar的命令所携带的参数

2. 在打包好的jar包的同级目录创建一个`config`目录在目录中添加的配置文件

3. 在打包好的jar包的同级目录创建的配置文件

4. 在项目源代码`/src/main/resources/config`目录下创建的配置文件

5. 在项目源代码`/src/main/resources`目录下创建的配置文件

具体的优先级如上，文件优先级如下（从左到右，优先级由高到低）

`application.properties` -> `application.yml` -> `application.yaml`

### 四级配置文件

SpringBoot中分为4级配置文件（自上而下，优先级由高到低）

1. file: config/application.yml
2. file: application.yml
3. classpath: config/application.yml
4. classpath: application.yml（Intllij IDEA中创建的配置文件，默认就是这级配置文件，优先级最低）
