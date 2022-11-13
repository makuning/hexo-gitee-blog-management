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

#### web-webpack-plugin
