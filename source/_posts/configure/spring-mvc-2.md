---
title: Spring-MVC配置（二）
cover: /img/website/cover-article-default.png
comments: true
categories:
  - 程序配置
tags:
  - spring
  - mvc
date: 2022-12-12 10:59:43
updated: 2022-12-12 10:59:43
---

# SpringMVC基础配置

上一节已经将SpringMVC的框架搭好了，就不再多说

## 接收参数

### url中的参数

请求路径为`http://127.0.0.1/user/test?name=john&age=10`

```java
// 请求的url地址
@RequestMapping("/user/test")
// 让方法返回的结果就作为响应的响应体，不做处理
@ResponseBody
// @RequestParam表示它为请求中的一个参数单元，不使用value属性，那么变量名一样要与参数名相同
public String save(@RequestParam(value = "name") String name,@RequestParam(value = "age") Integer age) {
    System.out.println("name:" + name + ",age:" + age);
    return "/user/test";
}
```

### 表单中的参数

与url中的参数的访问方式一样

```java
// 请求的url地址
@RequestMapping("/user/test")
// 让方法返回的结果就作为响应的响应体，不做处理
@ResponseBody
// @RequestParam表示它为请求中的一个参数单元，不使用value属性，那么变量名一样要与参数名相同
public String save(@RequestParam(value = "name") String name,@RequestParam(value = "age") Integer age) {
    System.out.println("name:" + name + ",age:" + age);
    return "/user/test";
}
```

### 数组

请求路径为`http://127.0.0.1/user/test?likes=game&likes=tv&likes=reading`

```java
// 请求的url地址
@RequestMapping("/user/test")
// 让方法返回的结果就作为响应的响应体，不做处理
@ResponseBody
// 如果使用list存储数组参数，那么一定要设置@RequsetParam
// 否则spring不会把list认为是一个参数，而是认为是用它的属性接收多个参数
public String save(@RequestParam(value = "likes") List<String> likes) {
    System.out.println(likes);
    return "/user/test";
}
```

### 用对象的属性接收

使用对象的属性接收，属性变量名必须与参数名相同，否则不会识别

请求路径为`http://127.0.0.1/user/test?name=john&age=20&balance=100&gender=male&address.province=sichuan&address.city=chengdu`

```java
// 请求的url地址
@RequestMapping("/user/test")
// 让方法返回的结果就作为响应的响应体，不做处理
@ResponseBody
// 使用user对象接收，参数名与对象的属性名需要一致
// 如果对象属性有引用类型，使用对象属性名.对象属性名进行传参
public String save(User user) {
    System.out.println(user);
    return "/user/test";
}
```

打印如下

```tex
User{id=null, name='john', gender='male', age=20, phone='null', birthday=null, balance=100.0, address=Address{province='sichuan', city='chengdu'}}
```

### 接收时间类型的参数

请求路径为`http://127.0.0.1/user/test?date=2001/06/24`

```java
// 请求的url地址
@RequestMapping("/user/test")
// 让方法返回的结果就作为响应的响应体，不做处理
@ResponseBody
// 使用@DateTimeFormat的pattern属性定义格式，将字符串转为日期类型
public String save(@DateTimeFormat(pattern = "yyyy/MM/dd") Date date) {
    System.out.println(date);
    return "/user/test";
}
```

打印如下

```tex
Sun Jun 24 00:00:00 CST 2001
```

## 全局配置

### 配置json数据的读取

导入jackson坐标用于json处理

```xml
<!-- jackson坐标 -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.0</version>
</dependency>
```

在org.example.config.SpringMvcConfig中开启功能

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
@ComponentScan({"org.example.controller"})
// 开启功能
@EnableWebMvc
public class SpringMvcConfig {
}
```

设置json格式的请求，在json格式的请求体中写入以下信息

```json
{
    "name":"马昆",
    "gender":"male",
    "age": 21,
    "phone":"19960798888",
    "birthday":"2001-06-24",
    "balance":100.00,
    "address": {
        "province":"四川",
        "city":"眉山"
    }
}
```

控制器接收

```java
// 请求的url地址
@RequestMapping("/user/test")
// 让方法返回的结果就作为响应的响应体，不做处理
@ResponseBody
// 使用@RequsetBody表示这是一个json格式的请求体
public String save(@RequestBody User user) {
    System.out.println(user);
    return "/user/test";
}
```

### json格式数据的发送

创建一个专属的消息类org.example.domain.JsonResult

```java
package org.example.domain;

/**
 * @author makun
 * @project spring
 * @description json格式的消息
 * @date 2022/12/12 14:27:51
 * version 1.0
 */
public class JsonResult {
    private Integer code;
    private String message;
    private Object data;

    public JsonResult() {
    }

    public JsonResult(Integer code, String message, Object data) {
        this.code = code;
        this.message = message;
        this.data = data;
    }

    @Override
    public String toString() {
        return "JsonResult{" +
                "code=" + code +
                ", message='" + message + '\'' +
                ", data=" + data +
                '}';
    }

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public Object getData() {
        return data;
    }

    public void setData(Object data) {
        this.data = data;
    }
}
```

更改控制器方法的返回值

```java
// 请求的url地址
@RequestMapping("/user/test")
// 让方法返回的结果就作为响应的响应体，不做处理
@ResponseBody
// 使用@RequsetBody表示这是一个json格式的请求体
// 因为导入了jackson的坐标，并且使用@EnableWebMvc开启了功能
// 使用@ResponseBody会自动将对象转化为json字符串
public JsonResult save(@RequestBody User user) {
    return new JsonResult(200, "配置成功！", user);
}
```

如果请求的格式正确，出现返回以下结果，时间格式传递的是一个时间戳

```json
{
    "code": 200,
    "message": "配置成功！",
    "data": {
        "id": null,
        "name": "马昆",
        "gender": "male",
        "age": 21,
        "phone": "19960798888",
        "birthday": 993340800000,
        "balance": 100.0,
        "address": {
            "province": "四川",
            "city": "眉山"
        }
    }
}
```

### 解析中文

在请求体中增加中文字段，出现以下情况

```json
{
    "code": 200,
    "message": "配置成功！",
    "data": "ä¸­æ"
}
```

在org.example.config.ServletControllerInitConfig中添加过滤器

```java
// 过滤器
@Override
protected Filter[] getServletFilters() {
    // springmvc有一个自带的处理字符的过滤器
    CharacterEncodingFilter encodingFilter = new CharacterEncodingFilter();
    // 设置字符集
    encodingFilter.setEncoding("UTF-8");
    return new Filter[]{encodingFilter};
}
```

重启服务再次访问，数据获取正常

```json
{
    "code": 200,
    "message": "配置成功！",
    "data": "中文"
}
```

