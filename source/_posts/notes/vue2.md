---
title: Vue2入门笔记
cover: https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2/cover.png
comments: true
categories:
  - 笔记
tags:
  - Vue入门
  - Vue2
  - 学习
date: 2022-11-13 13:35:28
updated: 2022-11-13 13:35:28
---

# [Bilibili黑马程序员Vue2](https://www.bilibili.com/video/BV1zq4y1p7ga/?vd_source=43f3f41b9a99cfe3d5248db59a3897c7)
基于[Bilibili黑马程序员Vue2+vue3](https://www.bilibili.com/video/BV1zq4y1p7ga/?vd_source=43f3f41b9a99cfe3d5248db59a3897c7)教程的学习笔记

## 前端工程化

- 模块化
- 组件化
- 规范化
- 自动化
比如开发时热部署

前端工程化具体解决方案：**webpack**（还有其他的，但是这个较为流行）

## webpack

webpack是前端项目工程化的具体解决方案。

能够压缩代码、处理浏览器端JavaScript兼容性、性能优化。

### webpack的基本使用

```bash
# 创建node项目
$ npm init -y

# 在项目根目录中创建src源代码目录
$ mkdir src

# 在src中创建index.html与index.js
$ cd src
$ touch index.html
$ touch index.js

# 安装JQuery
$ npm install npm jquery --save
$ npm install npm jquery -S
```

使用ES6导入JQuery

```js
// 导入jquery第三方包，并使用变量$接收
import $ from 'jquery'

$(function() {
  // 实现奇偶变色
  $('li:odd').css('background-color','red');
  $('li:even').css('background-color','pink');
});
```

在index.html中导入包含以上代码的index.js文件后，会出现语法错误，因为浏览器不支持ES6语法。可以使用webpack来帮我们解决。

```bash
# 安装webpack相关的两个包，--save-dev让包保存在devDependencies节点中
# --save-dev表示只在开发阶段使用到的包（4.7.2版本好像有问题，目前直接安装最新版本就能解决）
$ npm install webpack@5.42.1 webpack-cli@4.7.2 --save-dev
$ npm install webpack@5.42.1 webpack-cli@4.7.2 --D
```

在项目根目录中，创建名为webpack.config.js的webpack配置文件，初始化内容：

```js
// 向外导出一个webpack配置对象
module.exports = {
  mode: 'development'  // mode用来指定构建模式，development（开发） production（生产）
  // 使用development打包速度快，代码不会压缩，项目体积大
  // 使用production打包速度慢，代码会压缩，项目体积小
}
```

在package.json的scripts阶段下，新增dev脚本（也可以叫其他的名字）

```js
"scripts": {
  "dev": "webpack" // 使用npm run xxx执行对应脚本，如：npm run dev
}
```

使用`$ npm run dev`来使用webpack工具，它会先读取webpack.config.js这个文件，然后根据配置在项目根目录生成一个dist目标目录，其中有一个main.js脚本文件，并且这个main.js包含index.js与jquery.js的代码

然后再在index.html中引入main.js代码，就能够运行了，没有兼容性问题。

webpack4.x与5.x默认打包的入口文件为/src/index.js，默认输出文件的路径为/dist/main.js，可以在webpack.config.js配置文件中可以修改默认配置，通过entry节点可以指定导报的入口，通过output节点指定打包的出口。

```js
const path = require('path');

module.exports = {
  mode: 'development',
  entry: path.join(__dirname,'./src/index.js'),
  output: {
    path: path.join(__dirname,'./dist'),
    filename: 'bundle.js'
  }
}
```

### webpack中的插件

可以拓展webpack的能力

#### webpack-dev-server

每当修改了源代码，就会对项目进行构建和打包

使用`$ npm install webpack-dev-server@3.11.2 --save-dev`安装

安装后需要对package.json > script中的dev命令进行修改

```js
"scripts": {
  "dev": "webpack serve"
}
```

使用`$ npm run dev`运行项目，会发现程序一直在监听而不会退出，一旦源码进行修改项目就会重新打包。**每次重新部署生成的目标文件并不会放在物理磁盘上，而是在内存中，能够通过控制台提示的地址访问**

默认访问此插件提供的http服务地址，是访问项目根目录，为了能够打开地址能够直接访问index首页，可以将/src/index.html复制一份放在项目根目录中，这里就可以用**web-webpack-plugin**第三方插件来帮我们完成

#### web-webpack-plugin

使用`$ npm install html-webpack-plugin@5.3.2 -save-dev`安装此插件

在webpack.config.js配置文件中中配置html-webpack-plugin

```js
// 得到构造函数
const HtmlPlugin = require('html-webpack-plugin');

// 创建HTML插件实例对象
const htmlPlugin = new HtmlPlugin({
  template: './src/index.html',   // 指定源文件的存放路径
  filename: './index.html'        // 指定生成文件的存放路径
});

module.exports = {
  mode: 'development',
  plugins: [htmlPlugin]           // 通过plugins节点，使htmlPlugin插件生效
}
```

可以让webpack自动打开浏览器，也可以配置端口。需要在webpack.config.js的配置对象中配置devServer节点

```js
devServer: {
  open: true,
  host: '127.0.0.1',
  port: 80
}
```

### webpack中的loader（加载器）

webpack默认只能处理.js文件，为了处理其他文件就需要对应的loader

使用`$ npm install style-loader@3.0.0 css-loader@5.2.6 -D`安装处理CSS文件的加载器，然后在webpack.config.js配置对象的module节点增加rules节点

```js
module: {
  // 所有第三方模块的匹配规则
  rules: [
    {
      // 文件后缀为.css
      test: /\.css$/,
      // 使用到的插件
      use: [
        'style-loader',
        'css-loader'
      ]
    }
  ]
}
```

处理less文件

- Less是一门CSS预处理语言，css是一种用来表现HTML或XML等文件样式的计算机语言。
- less扩展了CSS语言，增加了变量、Mixin、函数等特性。
- css可以被浏览器直接识别，less需要先编译为css。

运行`$ npm install less-loader@10.0.1 less@4.1.1 -D`命令下载对应加载器（可以不用下载less，因为less-loader依赖less，会自动安装），并在webpack.config.js配置对象的module > rules数组进行配置。

```js
module: {
  // 所有第三方模块的匹配规则
  rules: [
    {
      // 文件后缀为.css
      test: /\.css$/,
      // 使用到的插件
      use: [
        'style-loader',
        'css-loader'
      ]
    },{
      test: /\.less$/,
      use: [
        'style-loader',
        'css-loader',
        'less-loader'
      ]
    }
  ]
```

小图片优化：base64，精灵图

运行`$ npm install url-loader@4.1.1 file-loader@6.2.0 -D`命令，在webpack.config.js配置对象的module > rules数组中添加规则

```js
module: {
  rules: [
    {
      test: '/\.jpg|png|gif$/',
      // ?后为参数项
      // limit指定图片大小，（byte），只有小于等于这个大小的图片才会被转为base64格式的图片
      use: 'url-loader?limit=2229'
    }
  ]
}
```

#### 处理高级语法

支持装饰器语法

运行`npm install babel-loader@8.2.2 @babel/core@7.14.6 @babel/plugin-proposal-decorators@7.14.5 -D`下载webpack对应的babel加载器和babel的插件

**@babel/core@7.14.6**

这个前面的@babel表示babel公司的私有包，/core表示这个公司名下的core第三方包，@7.14.6表示版本

```js
module: {
  rules: [
     {
          test: /\.js/,
          use: 'babel-loader',
          // 排除项，不处理第三方包
          exclude: /node_modules/
        }
  ]
}
```

以上只是匹配了规则，在项目根目录中还要创建babel.config.js的配置文件，来应用bale旗下的插件（插件的插件）

```js
module.exports = {
  // 生命babel可用的插件
  plugins: [
    ['@babel/plugin-proposal-decorators',{legacy: true}]
  ]
}
```

## build配置

目前配置后webpack只能将项目生成在内存中，如果想发布项目怎么办？

在package.json文件中的scripts中添加build节点

```json
"scripts": {
  "build": "webpack --mode production"
}
```

## 自动清理dist文件夹

使用`$ npm install clean-webpack-plugin --save-dev`命令进行安装

```js
const {CleanWebpackPlugin} = require('clean-webpack-plugin');

module.exports = {
	plugins: [
		new CleanWebpackPlugin()
	]
}
```

## Source Map

因为生成的代码与源代码不一致，就导致报错位置不能与源代码对应，Source Map保存信息文件，存储生成代码位置信息。在webpack.config.js的配置对象中添加节点`devtool: 'eval-source-map'`，只在开发时使用此工具。

### 只定位行号不暴露源码

`devtool: 'nosources-source-map'`

## webpack取别名

将@表示源码根目录，在webpack.config.js配置对象节点中添加以下内容

```js
resolve: {
	alias: {
		'@': path.join(__dirname,'./src/')
	}
}
```

## 安装浏览器vue调试工具

在Chrome安装vue调试工具

[下载浏览器插件](https://devtools.vuejs.org/)

更多 -> 更多工具 -> 扩展工具 -> 开发者模式 -> 安装插件

配置插件 -> 详情 -> 允许访问文件网址 

## Vue

用于构建用户界面的前端框架

需要学习vue框架的规范

vue的指令、组件、路由、Vuex、Vue组件库

### Vue的特性

#### 数据驱动视图

Vue将获取到的数据自动渲染到页面结构

#### 双向数据绑定

在网页中，form表单负责采集数据，ajax负责传递数据

#### 工作原理：MVVM

Model、View、ViewModel

Model：表示当前页面渲染时所依赖的数据源
View： 表示当前页面所渲染的DOM结构
ViewModel： 表示vue的示例，他是MVVM的核心

### Vue基本使用

```html
<!-- 引入Vue -->
<script src="https://cdn.jsdelivr.net/npm/vue@2.7.10/dist/vue.js"></script>

<!-- username表示接收传入数据的变量 -->
<div id="app">{{ username }}</div>

<script>
	const vm = new Vue({
		// 需要被控制的dom，选择器只会控制第一个被选择的元素
		el: '#app',
		// 传入的数据
		data: {
			username: 'zhangsan'
		}
	})
</script>
```

#### Vue指令

指令是为开发者提供的模板语法，用于辅助开发者渲染页面的基本结构

指令分为六大类

- 内容渲染指令
- 属性绑定指令
- 事件绑定指令
- 双向绑定指令
- 条件渲染指令
- 列表渲染指令

##### 内容渲染指令

用在内容节点的指令，指令的模板引擎不仅能插入数值，还能执行简单的javascript运算

###### v-text

会覆盖元素中所有的内容

```html
<div id="app">
	<p v-text="username"></p>
	<p v-text="gender"></p>
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			username: 'lisi',
			gender: 'man'
		}
	});
</script>
```
###### 插值表达式

插值表达式就是两个大括号，英文名Mustache，只是内容的占位符，不会覆盖原有的内容。

```html
<div id="app">
	<p>{{ username }}</p>
	<p>{{ gender }}</p>
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			username: 'lisi',
			gender: 'man'
		}
	});
</script>
```

###### `v-html`

`v-text`和插值表达式只能渲染文本内容，不能渲染html标签内容，`v-html`能解决这个问题

```html
<div id="app">
	<p v-html="info"></p>
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			info: '<h1>哈哈哈哈哈哈</h1>'
		}
	});
</script>
```

##### 属性渲染指令

用在属性节点的指令

###### `v-bind`

加在属性的前面并加一个冒号，也可省略`v-bind`，只加冒号

例：`v-bind:img="变量"`or`:img="变量"`

```html
<div id="app">
	<img v-bind:src="photo">
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			photo: 'http://xxx.com/xxx.img'
		}
	});
</script>
```

##### 事件绑定指令

###### `v-on`事件绑定

可以使用`@`来省略`v-on:`

```html
<div id="app">
	<p>{{ count }}</p>
	<button v-on:click="add">点击加</button>
	<button v-on:click="sub">点击减</button>
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			count: 0
		},
		methods: {
			add: function() {
				console.log('ok');
			},
			sub: function() {
				console.log('okk');
			}
		}
	});
</script>
```

**如何传参**

```html
<div id="app">
	<p>{{ count }}</p>
	<button v-on:click="add(2)">点击加</button>
	<button v-on:click="sub(1)">点击减</button>
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			count: 0
		},
		methods: {
			add: function(num) {
				console.log(num);
			},
			sub: function(num) {
				console.log(num);
			}
		}
	});
</script>
```

**使用`@`简写事件绑定指令**

```html
<div id="app">
	<p>{{ count }}</p>
	<button @click="add(2)">点击加</button>
	<button @click="sub(1)">点击减</button>
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			count: 0
		},
		methods: {
			add: function(num) {
				console.log(num);
			},
			sub: function(num) {
				console.log(num);
			}
		}
	});
</script>
```