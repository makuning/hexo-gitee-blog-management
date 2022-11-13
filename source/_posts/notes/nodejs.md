---
title: NodeJS入门笔记
cover: https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/nodejs/cover.png
comments: true
categories:
  - 笔记
tags:
  - NodeJS入门
  - NodeJS
  - 学习
date: 2022-11-07 22:35:11
updated: 2022-11-08 12:43:11
---

# [Bilibili黑马程序员NodeJS](https://www.bilibili.com/video/BV1a34y167AZ)

基于[Bilibili黑马程序员NodeJS教程](https://www.bilibili.com/video/BV1a34y167AZ)的学习记录

## 什么是nodejs
nodejs是基于谷歌v8引擎的js解释器，nodejs包含npm，npm是node包管理工具

## 如何运行js代码
```bash
# 执行当前目录下名为xx.js的js源文件
$ node xx.js
```

## 内置模块
nodejs也和chrome的v8一样，有一些内置模块供我们使用

- fs文件模块
```js
// 引入fs模块
const path = require('fs');
```

- path路径模块
```js
// 引入path模块
const path = require('path');
```

- http服务模块
```js
// 引入http模块
const http = require('http');
```

## 模块化
```js
// 1、标准
module.exports = {

};

// 或者
exports.xx = xxx;

// 2、简洁
exports.xx = xxx;
```

## npm使用
```bash
# 因为npm默认下载包的地址在国外，可以切换国内的镜像源
# 下载镜像源快速切换工具nrm
$ npm install -g nrm

# 查看镜像源列表
$ nrm ls

# 使用列表中的镜像，例：
$ nrm use taobao

# 下载某个包
$ npm install xxxx[@version] [--save]

# 卸载某个包
$ npm uninstall xxxx[@version] [--save]
```

## express
优秀的第三方模块，能够快速开发中间件

### 路由
```js
// 文件1
const express = require('express');
const Router = express.Router();
Router.get('path1',func1);
Router.post('path2',func2);
module.exports = Router;

// 文件2
const express = require('express');
const Router = require('./文件1');
const app = express();

app.use('前缀',Router);

app.listen(端口,funciton;
```

### 中间件
express在收到请求时会先调用中间件

中间件的调用会根据中间件注册的顺序执行，而且会共用一个res和req，也就是说可以在上游中间件给res对象和req对象加入一些属性或方法供下游中间件使用。

```js
const express = require('express');
const app = express();

// 创建中间件
function mw1(req,res,next) {
    /*
    你的代码
    */
	next();
}
function mw2(req,res,next) {
	/*
	你的代码
	*/
	next();
}

// 注册全局生效中间件
app.use(mw1);

// 注册局部生效的中间件
app.get('path',mw2,func);

// 注册多个局部生效的中间件
app.get('path',[mw1,mw2],func);
// or
app.get('path',mw1,mw2,func);
```
**注意事项**
- 中间件的注册应当放在路由的前面
- 中间件函数中的业务完成后应当调用next函数，否则请求会停止在此中间件
- 中间件函数应当最后调用next函数，不能在next函数调用后还继续写代码，否则会造成代码混乱

### 中间件的分类

- 应用级别中间件
注册在app上的中间件
```js
const express = require('express');
const app = express();

const mw = (req,res,next) => {
	next();
}

// 直接注册在app上
app.use(mw);

// 注册在app的路由上
app.get('path',mw,func);
```

- 路由级别中间件

注册在Router上的中间件
```js
const express = require('express');
const Router = express.Router();

const mw = (req,res,next) => {
	next();
}

Router.use(mw);
```

- 错误级别中间件

用于处理错误，防止系统崩溃的中间件，一定要在路由后注册
```js
const express = require('express');
const app = express();

// 定义错误中间件，相较于普通中间件多了一个err参数
const mw = (err,req,res,next) => {
	next();
}

app.get('path',func);

// 在路由后注册才会有err对象，否则err为undefined
app.use(mw);
```

- 内置中间件

express自带的中间件

- 内置中间件-express.static()

快速托管静态资源
```js
const express = require('express');
const app = express();

app.use('prefix',express.static('path'));
```
- 内置中间件-express.json()

解析请求体中的json数据
```js
const express = require('express');
const app = express();

app.use(express.json());

// 通过req对象的body属性获取
app.get('path',(req,res) => {
	let body = req.body;
	console.log(body);
	res.send(body);
});
```

- 内置中间件-express.urlencoded()

解析请求体中url-www-extended（好像叫这个吧，忘了）的数据
```js
const express = require('express');
const app = express();

// 传入一个对象（我也不太清楚什么意思）
app.use(express.urlencoded({
	extended: false;
}));

// 通过req对象的body属性获取
app.get('path',(req,res) => {
	let body = req.body;
	console.log(body);
	res.send(body);
});
```

- 第三方中间件

别人写的优秀的第三方中间件，例如body-parser，但是express@4.16.0以后就自带了这个东西
就是express.urlencoded()，它是基于body-parser的封装
首先要下载body-parser包
```bash
$ npm install body-parser --save
```
使用方法与express.encoded()差不多
```js
const express = require('express');
const bodyParser = require('body-parser');
const app = express();

// 传入一个对象（我也不太清楚什么意思）
app.use(bodyParser.urlencoded({
	extended: false;
}));

// 通过req对象的body属性获取
app.get('path',(req,res) => {
	let body = req.body;
	console.log(body);
	res.send(body);
});
```

## 跨域问题

**什么是跨域问题**

在浏览器控制台中会出现类似以下的报错
```text
Access to XMLHttpRequest at 'http://127.0.0.1/api/get?id=2206831544&name=%E9%A9%AC%E6%9F%90&gender=%E7%94%B7' from origin 'null' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.
```
协议、域名、端口号的不同导致的跨域问题

**解决方案**

CORS（主流的解决方案，推荐使用）
JSONP（有缺陷的解决方案，只支持get请求）

### 使用cors第三方中间件 

cors是express解决跨域问题的第三方中间件

```bash
# 下载cors第三方包
$ npm install cors --save
```

```js
const express = require('express');
const cors = require('cors');
const app = express();

// 在引入路由之前注册cors中间件
app.use(cors());

// 在此写入路由
```
**指定域名通过跨域请求**

```js
// 指定http://19maken.top才能跨域访问
res.setHeader('Access-Control-Allow-Origin','http://19maken.top');

// 指定所有域名都能跨域访问
res.setHeader('Access-Control-Allow-Origin','*');

// 指定请求允许的请求方法
res.setHeader('Access-Control-Allow-Methods','POST,GET,DELETE,HEAD');

// 指定允许所有的请求
res.setHeader('Access-Control-Allow-Methods','*');
```

预检请求
有些请求不是简单请求，会先发送OPTION请求进行预检

**jsonp**

这个东西我也不太清楚，说是要如果配置了cors，就要配置在cors的前面，然后前端会使用script标签来解析响应的内容。

## mysql模块
这也是第三方模块，用于数据库的连接与操作。
```bash
# 下载第三方包
$ npm install mysql
```
导入mysql模块并使用
```js
// 导入mysql第三方包
const mysql = require('mysql');

// 创建连接
const connection = mysql.createConnection({
	host: 'localhost',
	user: 'user',
	password: 'secret',
	database: 'database'
});

// 连接
connection.connect();

// 操作连接
const sql = 'SELECT 1+1 AS solution';
connection.query(sql,function(error,results,fields) {
	if (error) throw error;
	console.log('The solution is:', results[0],solution);
});

// 关闭连接
connection.end();
```
使用带占位符的sql语句，PS：占位符使用?
```js
// 1、多个占位符，使用数据传数据
const sql = 'SELECT * FROM table WHERE age > ? and gender = ?';
connection.query(sql,[18,'男'],function(error,results,fields) {
	if (error) throw error;
	console.log(results);
});

// 2、如果占位符太多，可以使用对象
const sql = 'INSERT INTO table (fields) values (?)';
const data = {
	field1: field1,
	field2: field2,
	...
}
connection.query(sql,data,function(error,results,fields) {
	if (error) throw error;
	// 查看受影响的行数
	console.log(results.affectedRows);
});

// 3、如果只有一个占位符，那么就可以不用使用数组传数据，直接使用数据
const sql = 'SELECT * FROM table WHERE age > ?';
connection.query(sql,18,function(error,results,fields) {
	if (error) throw error;
	console.log(results);
});
```
**注意事项**

在执行操作的时候，如果执行的语句是查询语句，那么results就是一个数组，如果执行的是一个更新语句或者是删除语句，那么results就是一个对象，其中results的affectedRows属性可以查看执行后的语句所影响的行数。

## Web开发模式

SEO?

服务端渲染

对SEO友好，但对服务器压力过大

前后端分离

服务器压力小，不用渲染页面，前端注重页面，后端注重API，对SEO不友好，但能使用VUE等框架提供的服务

如何选择？

不同的场景使用不同的模式，例如管理系统不怎么需要SEO，就可以使用前后端分离。有些网站也采用首页使用服务端渲染，其他页面使用前后端分离。

**身份验证**

- 服务端渲染推荐使用Session认证机制。

使用Cookie，它是存储在用户浏览器中一段不超过4KB的字符串，由一个名称（name）、一个值（value）和其他几个用于控制Cookie有效期、安全性和使用范围的可选属性组成。每当发起请求，会将未过期的Cookie一同发送到对应域名的服务器。服务端可以通过响应头发送Cookie给客户端。但是Cookie不安全，因为谁都可以看，会很好仿造。所以使用Cookie+Session认证。**因为Session中的数据是对应客户端的**客户端只能访问属于自己的session数据，而不能访问别的客户端的session数据。

- 前后端分离推荐使用JWT认证机制。

## 在Express中使用Session认证
下载express-session中间件
```bash
$ npm install express-session
```
注册express-session中间件
```js
var express = require('express');
var session = require('express-session');
var app = express();

app.use(session({
	secret: 'keyboard cat',		// secret属性的值可以为任意字符串
	resave: false,				// 固定写法
	saveUninitialized: true		// 固定写法
}));

app.get('/user/login',function(req,res) {
	// 往session中存储数据
	req.session.xxx1 = xxx1;
	req.session.xxx2 = xxx2;
	res.send('success');
});

app.get('/user/getXxx',function(req,res) {
	// 取session中的数据
	res.send(req.session.xxx1 + '\n' + req.session.xxx2);
});

app.get('/user/logout',function(req,res) {
	// 清空Session中的数据
	req.session.destroy();
	res.send('success');
});
```
## JWT跨域认证

由于Cookie默认不支持跨域，如果使用Session+Cookie作为身份验证机制，配置会很麻烦。JWT（JSON Web Token）是目前最流行的跨域认证解决方案。

**JWT工作流程**

首先客户端（浏览器）会发送登陆请求，服务器接收到请求后先验证账号和密码，验证通过后会将用户的信息对象经过加密后生成Token字符串（服务端不会存储这个字符串），然后响应给客户端，客户端收到响应后会将Token存放在浏览器的LocalStorage或者SessionStorage中，当客户端再次发送请求的时候会通过请求头的Authorization字段将Token发送给服务器，服务器验证这个Token是否合法，解析为之前打包所对应的数据，然后针对数据发送对应的响应。**JWT中不要携带密码信息**

JWT组成部分，三部分，使用“.”分隔

Header(头部).Payload(有效荷载).Signature(签名)

Header与Signature只与安全有关，防止破解。Payload是加密后的用户信息。

**客户端如何使用JWT？**

```text
# 推荐做法：将JWT放在HTTP请求同的Authorization字段中。
Authorization: Bearer <token>
```

## 在Express中使用JWT

安装JWT相关的包

jsonwebtoken 用于生成JWT字符串

express-jwt 用于将JWT字符串解析还原成JSON数据包
```bash
# 安装如下两个JWT相关的包
$ npm install jsonwebtoken express-jwt
```
定义secret密钥：用于JWT加密与解密，建议命名为secretKey

使用方法

```js
const express = require('express');
const jwt = require('jsonwebtoken');
// 新版使用方法(7.7.7)，expressJWT为接收变量名，可任意更换
var {expressjwt: expressJWT} = require('express-jwt');
const app = express();

// 定义secret密钥（随意字符串）
const secretKey = 'XXXX xxxx II';

// 配置JWT解析中间件
	// 这个algorithms必须配置，好像是配置加密算法
// expressJWT({})：配置解析
// expressJWT({}).unless({})：配置哪些不使用解析
// 添加expressJWT中间件后，express会将解析的token信息挂载在req.user中
app.use(expressJWT({
	secret: secretKey,
	algorithms: ["HS256"]
}).unless({
	path: [/^\/api\//]
}));
// 解析请求体JSON数据
app.use(express.json());

app.post('/api/user/login/action',function(req,res) {
	const user = req.body;
	if (user.name === 'admin' && user.pwd === '123456') {
		console.log('登陆成功！');
		// 参数1：加密信息，必须为一个对象！
		// 参数2：加密密钥
		// 参数3：配置，有很多参数，这里只配置了token有效期为30秒
		const tokenStr = jwt.sign({username: user.name},secretKey,{expiresIn: '30s'});

		res.send({
			status: 200,
			message: '登陆成功！',
			token: tokenStr
		});
	} else {
		console.log('登陆失败！');
		res.send({
			status: 200,
			message: '登陆失败！',
			token: null
		});
	}
});

// 请求此接口时应该在请求头的Authorization中添加token信息
app.get('/user/info',function(req,res) {
	const user = req.user;
	// username为之前生成token时需要打包的数据中的字段
	if (user.username === admin) {
		res.send({
			code: 200,
			msg: '请求成功！',
			data: {
				name: 'admin',
				age: 19
			}
		});
	}
});

// 添加token解析异常中间件处理
app.use((err,req,res,next) => {
	if (err.name === 'UnauthorizedError') {
		return res.send({
			code: 401,
			msg: '无效的token',
		});
	} 

	// 如果不是token错误
	res.send({
		code: 500,
		msg: '未知的错误',
	});
});
```