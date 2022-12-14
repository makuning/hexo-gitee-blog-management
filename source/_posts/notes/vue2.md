---
title: Vue2入门笔记（1）
cover: https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/notes/vue2/cover.png
comments: true
categories:
  - 笔记
tags:
  - Vue入门
  - Vue2
  - 学习
date: 2022-11-13 13:35:28
updated: 2022-11-25 15:21:28
---

# [Bilibili黑马程序员Vue2](https://www.bilibili.com/video/BV1zq4y1p7ga/?vd_source=43f3f41b9a99cfe3d5248db59a3897c7)
基于[Bilibili黑马程序员Vue2+vue3](https://www.bilibili.com/video/BV1zq4y1p7ga/?vd_source=43f3f41b9a99cfe3d5248db59a3897c7)教程的学习笔记（1）

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

Vue有内置对象，$event表示js原生事件对象

```html
<div id="app">
	<p>{{ count }}</p>
	<button @click="add($event,2)">点击加</button>
	<button @click="sub(1)">点击减</button>
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			count: 0
		},
		methods: {
			add: function(e,num) {
				console.log(e.target);
				console.log(num);
			},
			sub: function(num) {
				console.log(num);
			}
		}
	});
</script>
```

**事件修饰符**

加在事件绑定的后面

| 事件修饰符 | 说明                                               |
| :--------- | :------------------------------------------------- |
| .prevent   | 阻止默认行为                                       |
| .stop      | 阻止事件冒泡                                       |
| .capture   | 以捕获模式出发当前的事件处理函数                   |
| .once      | 绑定的事件只触发一次                               |
| .self      | 只有在event.target是当前元素自身时触发事件处理函数 |

**阻止默认事件**

`@click.prevent="show()"`

**按键修饰符**

| 按键修饰符 | 说明                    |
| :--------- | :---------------------- |
| .enter     | 按键是enter时才触发事件 |
| .esc       | 按键时esc时才触发事件   |
| ...        | ...                     |

##### 双向绑定指令

使用`v-model`设置双向绑定数据，dom能够改变变量，变量也能改变dom

`v-bind`即`:`是单向绑定，不会改变变量的值，只会变量改变dom

```html
<div id="app">
	<input type="text" v-model="content" >
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			content: 'hello world'
		}
	});
</script>
```

**在表单元素中使用才有意义**

`v-model`指令的修饰符

| `v-model`修饰符 | 说明                           |
| :-------------- | :----------------------------- |
| .number         | 识别绑定的数据为数值           |
| .trim           | 自动去除首尾空格               |
| .lazy           | 中间的变化过程不会同步到变量中 |

##### 条件渲染指令

按需控制DOM的显示与隐藏

###### `v-if`

每次动态创建或移除元素

如果刚进入页面，某些页面默认不需要被展示，而且后期也很可能不需要被展示出来，这时`v-if`性能更好

```html
<div id="app">
	<p v-if="flag">这是被v-if控制的元素</p>
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			flag: true
		}
	});
</script>
```

###### `v-show`

是动态为元素添加`display: none`样式，来实现元素的显示和隐藏

如果频繁显示隐藏，用`v-show`的性能要高

```html
<div id="app">
	<p v-show="flag">这是被v-show控制的元素</p>
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			flag: true
		}
	});
</script>
```

###### `v-else`&`v-else-if`

```html
<div v-if="<条件>">优秀</div>
<div v-else-if="<条件>">良好</div>
<div v-else-if="<条件>">一般</div>
<div v-else>差</div>
```

##### 列表渲染指令（循环指令`v-for`）

使用语法`item in items`，其中`in`是固定指令，items接收data中的数组，item接收items中的元素

```html
<table id="app">
	<thead>
		<tr>
			<th>姓名</th>
			<th>性别</th>
		</tr>
	</thead>
	<tbody>
		<tr v-for="item in items" :title="item.name">
			<td>{{ item.name }}</td>
			<td>{{ item.gender }}</td>
		</tr>
	</tbody>
</table>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			item: [
				{
					name: '张三',
					gender: '男'
				},
				{
					name: '李四',
					gender: '女'
				}
			]
		}
	});
</script>
```

使用语法`(item,index) in items`，其中`in`是固定指令，items接收data中的数组，item接收items中的元素，index是元素下标，从0起标

```html
<table id="app">
	<thead>
		<tr>
			<th>序号</th>
			<th>姓名</th>
			<th>性别</th>
		</tr>
	</thead>
	<tbody>
		<tr v-for="(item,index) in items" :title="item.name + (index + 1)">
			<td>{{ index + 1 }}</td>
			<td>{{ item.name }}</td>
			<td>{{ item.gender }}</td>
		</tr>
	</tbody>
</table>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			item: [
				{
					name: '张三',
					gender: '男'
				},
				{
					name: '李四',
					gender: '女'
				}
			]
		}
	});
</script>
```

**官方建议：**只要用到了`v-for`指令，那么一定要绑定一个`:key`属性，并且尽量把`item.id`作为`:key`的值，这个值只能是字符串或者是数字类型，`:key`的值不能重复，否则会报错`Buplicat keys detected`

使用index的值作为key的值并没有任何意义，因为这个index与内容并没有绑定关系，他是和元素顺序有关

指定key的值既能提升性能、又防止列表状态絮乱

```html
<table>
	<tr v-for="(item,index) in items" :key="item.id">
		<td>{{ item.name }}</td>
	</tr>
</table>
```

#### Vue过滤器（Filters）

常用于文本格式化，可和`v-bind`属性绑定，管道符`|`

前面的变量作为管道后面过滤函数的参数，过滤器函数应定义到filters节点下

**私有过滤器**

```html
<div id="app">
	<p>{{ message | toUp }}</p>
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			message: 'hello'
		},
		filter: {
			toUp: function(message) {
				console.log(message);
				return message + 1;
			}
		}
	});
</script>
```

**全局过滤器**

```html
<script>
	Vue.filter('capi',function(str) {
		const first = str.charAt(0).toUpperCase();
		const other = str.slice(1);
		return first + other;
	});
</script>
```

过滤器也能够串联调用，例：`item.time | dateformat | xxx | yyy | zzz`

#### Vue侦听器（watch）

定义在watch节点下，方法名与需要监听的数据变量名一致，监视数据的变化

**方法格式侦听器**

```html
<div>
	<input type="text" v-model="username" >
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			username: ''
		},
		watch: {
			username(newVal,oldVal) {
				console.log('新值：' + newVal);
				console.log('旧值：' + oldVal);
				console.log('监听到了username值的变化');
			}
		}
	});
</script>
```

以上的方法侦听器无法在初始化页面时就执行一次，可以使用对象格式实现自动触发一次

也无法监听对象中的属性变化，可以使用对象格式的deep选项

**对象格式侦听器**

```html
<div id="app">
	<input type="text" v-model="username" >
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			username: ''
		},
		watch: {
			username: {
				// 侦听器处理函数
				handler (newVal,oldVal) {
                    console.log('新值：' + newVal);
                    console.log('旧值：' + oldVal);
                    console.log('监听到了username值的变化');
				}
				// 控制侦听器是否触发一次，默认为false
				immediate: true
			}
		}
	});
</script>
```

**深度监听**

```html
<div id="app">
	<input type="text" v-model="info.username" >
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			info: {
				username: 'admin'
			}
		},
		watch: {
			info: {
				handler(newVal,oldVal) {
					console.log(newVal);
				},
				// 开启深度监听，只要对象中任何一个属性变化，都会触发对象侦听器，默认为false
				deep: true
			}
		}
	});
</script>
```

**直接侦听对象中的某个属性**

方法名包裹单引号

```html
<div id="app">
	<input type="text" v-model="info.username" >
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			info: {
				username: 'admin'
			}
		},
		watch: {
			'info.username'(newVal) {
				console.log(newVal);
			}
		}
	});
</script>
```

#### Vue计算属性

通过一系列运算后，最终得到的属性值（实现代码复用），放在computed节点下

依赖的属性变化后会自动重新求值

```html
<div id="app">
	r:<input type="text" v-model="r" ><br />
	g:<input type="text" v-model="g" ><br />
	b:<input type="text" v-model="b" ><br />
	<div :style="'background-color:' + rgb">
		<p v-text="rgb"></p>
	</div>
	<button @click="show">按钮</button>
</div>

<script>
	const vm = new Vue({
		el: '#app',
		data: {
			r: 100,
			g: 100,
			b: 100
		},
		methods: {
			show() {
				console.log(this.rgb);
			}
		},
		// 所有的计算属性，都要定义到computed节点下
		// 计算属性在定义的时候，要定义成方法格式
		computed: {
			// rgb作为一个计算属性，被定义成了方法格式
			// 最终要返回一个生成好的rgb(x,y,z)格式字符串
			rgb() {
				return `rgb(${this.r},${this.g},${this.b})`;
			}
		}
		
	});
</script>
```

#### axios

axios是一个专注于网络请求的库，jQuery还有很多其他的功能，过于庞大

cdn`<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>`

```js
// axios的基本用法
axios({
	method: '请求的类型',
	url: '请求的url地址'
}).then((result) => {
	// 处理函数
	console.log(result.data);
})
```

axios给收到的响应套了一层壳

- config
- data:{}	真实的数据
- headers
- request
- status
- statusText

**axios传参**

```js
aixos({
	// 请求方式
	method: 'GET',
	url: 'http://www.baidu.com',
	// url参数
	params: {
		id: 1
	},
	// 请求体参数
	data: {
		password: '13424'
	}
}).then((result) => {
	console.log(result.data);
})
```

**axios返回的是Promise对象**

[Promise参考博文](https://blog.csdn.net/weixin_41817034/article/details/80492315)

```js
document.querySelector('#btnPost').addEventListener('click', async function() {
	// 如果调用某个方法返回值是Promise实例，则前面可以添加await
	// await只能用在被async“修饰的方法中”
	
	// 解构后重命名
	const { data: res } = await axios({
		method: 'POST',
		url: 'http://www.liulongbin.top:3006/api/post',
		data: {
			name: 'zs',
			age: 20
		}
	})
	
	console.log(res.data);
})
```

**axios直接发送请求**

```js
axios.get('url地址',{
	// 参数
	params: {}
})

axios.post('url地址',{post请求体数据})
```














































