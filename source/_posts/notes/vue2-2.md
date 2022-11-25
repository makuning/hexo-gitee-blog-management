---
title: Vue2入门笔记（2）
comments: true
cover: https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2-2/cover.png
categories:
  - 笔记
tags:
  - Vue入门
  - Vue2
  - 学习
date: 2022-11-25 15:31:48
updated: 2022-11-25 15:31:48
---

# [Bilibili黑马程序员Vue2](https://www.bilibili.com/video/BV1zq4y1p7ga/?vd_source=43f3f41b9a99cfe3d5248db59a3897c7)
基于[Bilibili黑马程序员Vue2+vue3](https://www.bilibili.com/video/BV1zq4y1p7ga/?vd_source=43f3f41b9a99cfe3d5248db59a3897c7)教程的学习笔记（2）

## Vue-cli

什么是单页面应用程序，SPA（Single Page Application）

所有的功能与交互都在这唯一的一个页面内完成。

vue-cli是Vue.js开发的标准工具。简化了程序员基于webpack创建工程化的Vue项目的工程。

[中文官网](https://cli.vuejs.org/zh/)

### 安装和使用

它是npm上的一个全局包使用`npm install -g @vue/cli`来进行安装，安装完成后使用`vue --version`来查看是否安装成功

**创建项目**

```bash
# 创建项目
$ vue create <项目名>
```

会出现如下提示，并选择手动

```bash
Vue CLI v5.0.8
? Please pick a preset:
  # vue3预设
> Default ([Vue 3] babel, eslint)
  # vue2预设
  Default ([Vue 2] babel, eslint)
  # 手动选择要安装哪些功能
  Manually select features
```

使用空格选择与取消部分选项，然后回车

![选择需要的下载](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221125163616553.png)

选择vue2.x，然后回车

![选择vue2.x](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221125163924064.png)

选择Less，回车

![选择Less](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221125164020628.png)

选择第一项，将babel等插件的配置项，放到自己独立的文件中，回车

![选择独立配置](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221125164240909.png)

输入y保存我们的选项作为预设，回车

![保存预设](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221125164417096.png)

输入为预设取的别名，然后回车

![为预设取别名](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221125164516315.png)

等待加载完成后，进入项目目录，执行`npm run serve`命令让项目跑起来，打开控制台打印的url地址，查看效果。

### src目录和构成

```text
assets文件夹：存放项目中用到的静态资源文件，例如：css样式表、图片资源
components文件夹：程序员封装的、可复用的组件，都要放到componts目录下
main.js文件：项目的入口，整个项目的运行，要先执行main.js
App.vue：是项目的根组件
```

### vue项目的运行流程

通过main.js把App.vue渲染到index.html的指定区域中

1. App.vue 用来编写待渲染的模板结构
2. index.html中需要预留一个**el区域**
3. mian.js把App.vue渲染到了index.html所预留的区域中

```js
import Vue from 'vue'
import App from './App.vue'

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app')

// $mount('#app')就相当于el: '#app'
```

## vue组件

只要是`.vue`结尾的文件，就是vue组件

### vue组件的三个组成部分

1. template	组件的模板结构
2. script         组件的JavaScript行为
3. style          组件的样式

**script**

```vue
<template>
	<div class="test-box">
        <h3>这是用户自定义的Test.vue---{{ username }}</h3>
        <button @click="changeName">修改用户名</button>
    </div>
</template>

<script>
	// 默认导出，这是固定写法
    export default {
        // .vue组件中的data不能像之前一样，不能指向对象。
        // 组件中的data必须是一个函数
        /*data: {
            username: '张三'
        }*/
        data() {
            // 这个return出去的对象中，可以定义数据
            return {
                username: '张三'
            }
        },
        method: {
            changeName() {
                // 在组件中，this就表示当前组件的实例对象
                console.log(this);
                this.username = '李四';
            }
        },
        // 当前组件中的侦听器
        watch: {
            
        },
        // 当前组件中的计算属性
        computed: {
            
        },
        // 当前组件的过滤器
		filters: {
        
    	}
    }
</script>

<style>
    .test-box {
     	background-color: pink;
    }
</style>
```

**在template节点下只能有一个根元素**

```vue
<!-- 错误示范 -->
<template>
	<div class="test-box">
        <h3>这是用户自定义的Test.vue---{{ username }}</h3>
        <button @click="changeName">修改用户名</button>
    </div>
	<div>
        
    </div>
</template>

<!-- 正确示范 -->
<template>
	<div>
        <div class="test-box">
        	<h3>这是用户自定义的Test.vue---{{ username }}</h3>
        	<button @click="changeName">修改用户名</button>
    	</div>
        <div>
            
    	</div>
    </div>
</template>
```

**在style节点支持less语法**

```vue
<!-- 支持less -->
<style lang="less">
</style>

<!-- 不支持less，支持css，可不写 -->
<style lang="css">
</style>
```





















































































