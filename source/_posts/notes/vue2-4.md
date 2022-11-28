---
title: Vue2入门笔记（4）
cover: https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2-4/cover.png
comments: true
categories:
  - 笔记
tags:
  - Vue入门
  - Vue2
  - 学习
date: 2022-11-27 15:04:46
updated: 2022-11-28 13:04:46
---

# [Bilibili黑马程序员Vue2](https://www.bilibili.com/video/BV1zq4y1p7ga/?vd_source=43f3f41b9a99cfe3d5248db59a3897c7)

基于[Bilibili黑马程序员Vue2+vue3](https://www.bilibili.com/video/BV1zq4y1p7ga/?vd_source=43f3f41b9a99cfe3d5248db59a3897c7)教程的学习笔记（4）

## 路由

路由就是对应关系，Hash地址（锚链接）与组件之间的对应关系

使用锚链接不会导致页面刷新，并且能产生浏览历史

URL地址从`#`开始，`#`加它后面的部分就是Hash地址

通过`window.location.hash`可以查看页面的hash地址

### 前端路由的工作方式

1. 用户点击了页面上的路由链接

2. 导致了URL地址栏中的Hash值发生了变化

3. 前端路由监听了到Hash地址的变化

4. 前端路由把当前Hash地址对应的组件渲染都浏览器中

![image-20221127164723111](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2-4/image-20221127164723111.png)

原生JS使用`window.onhashchange`就能监听页面hash地址的变化

### vue-router

vue-router是vue.js官方给出的路由解决方案。它只能结合vue项目进行使用，能够轻松的管理SPA项目中组件的切换。

[官网地址](https://router.vuejs.org/zh/)

1. 安装vue-router包
   1. `npm install vue-router@3.5.2 -S`

2. 创建路由模块

   1. 在src 源代码目录下，新建router/index.js路由模块，并初始化如下的代码:

       ```js
       // 1．导入Vue和VueRouter 的包
       import Vue from 'vue'
       import VueRouter from 'vue-router'
       
       // 2．调用Vue.use()函数，把 VueRouter安装为Vue的插件
       Vue.use(VueRouter)
       
       // 3．创建路由的实例对象
       const router = new VueRouter()
       
       // 4．向外共享路由的实例对象
       export default router
       ```

3. 导入并挂载路由模块

   1. 在main.js中挂载路由 
      ```js
      // index.js可以省略
      // 在模块化导入的时候，如果只给了路径，则默认导入这个文件夹下，名字叫做index.js的文件
      import router from '@/router/index.js'
      
      new Vue({
          render: h => h(App),
          // 路由的实例对象
          router
      }).$mount('#app')
      ```

4. 声明路由链接和占位符

   1. 只要在项目中安装和配置了vue-router，就可以使用router-view这个组件了，用作占位符

      ```vue
      <template>
      	<div>
              <a href="#/home"></a>
              <a href="#/movie"></a>
              <a href="#/about"></a>
          </div>
      	<router-view></router-view>
      </template>
      ```

      ```js
      // router/index.js
      
      import Home from '@/components/Home.vue'
      import Movie from '@/components/Movie.vue'
      import About from '@/components/About.vue'
      
      const router = new VueRouter({
          // routes是一个数组，作用:定义“hash地址”与“组件之间的对应关系
          routes: [
              { path: '/home', componnent: Home},
              { path: '/movie', componnent: Movie},
              { path: '/about', componnent: About}
          ]
      })
      ```
### router-link

当安装和配置了vue-router后，就可以使用router-link来替代普通的a链接了

```vue
<template>
	<router-link to="/home">首页</router-link>
    <router-link to="/movie">首页</router-link>
    <router-link to="/about">首页</router-link>
</template>
```

### 路由重定向

路由重定向指的是:用户在访问地址A的时候，强制用户跳转到地址C，从而展示特定的组件页面。通过路由规则的redirect属性，指定一个新的路由地址，可以很方便地设置路由的重定向:

```js
const router = new VueRouter({
    routes: [
        // 使用重定向
        { path: '/', redirect: '/home'},
        { path: '/home', componnent: Home},
        { path: '/movie', componnent: Movie},
    ]
})
```

### 嵌套路由

通过路由实现组件的嵌套展示，叫做嵌套路由。

![image-20221128114341148](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2-4/image-20221128114341148.png)

通过children属性声明子路由规则

![image-20221128114525533](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2-4/image-20221128114525533.png)

### 动态路由

动态路由指的是:把Hash地址中可变的部分定义为参数项从而提高路由规则的复用性。

在vue-router中使用英文的冒号（:）来定义路由的参数项。示例代码如下:

```js
//路由中的动态参数以︰进行声明，冒号后面的是动态参数的名称
// 如何在Movie组件中获取Movie的ID值
{ path: '/movie/:id' , component: Movie }
```

在Movie组件中可以使用`this.$route.params.id`获取哈希地址名为id的动态地址参数

`this.$route`是路由的参数对象

`this.$router`是路由的“导航对象”

### 开启props传参

```js
// 在路由模块中定义，开启props
{ path: '/movie/mid', component: Movie, props: true}

// 在组件中接收
export default {
    props: ['mid']
}
```

### params和query

路径参数和查询参数

1. `/`后面的是路径参数，使用`this.$route.params`来获取
2. `?`后面的是查询参数，使用`this.$route.query`来获取

3. 需要完成路径使用`this.$route.fullPath`，只要路径使用`this.$route.path`

### 编程式导航

在浏览器中，点击链接实现导航的方式，叫做声明式导航。例如:

普通网页中点击`<a>`链接、vue项目中点击`<router-link>`都属于声明式导航

在浏览器中，调用API方法实现导航的方式，叫做编程式导航。例如:

普通网页中调用`location.href`跳转到新页面的方式，属于编程式导航

**vue-router提供了许多编程式导航的API，（router是导航对象），其中最常用的导航API分别是:**

1. `this.$router.push('hash地址')`
   1. 跳转到指定hash 地址，并增加一条历史记录
2. `this.$router.replace('hash地址')`
   1. 跳转到指定的hash地址，并替换掉当前的历史记录
3. `this.$router.go(数值n)`
   1. 可以在浏览历史中前进和后退。
   2. 如果后退的层数超过上限，则原地不动

在实际开发中，一般只会前进和后退一层页面。因此 vue-router提供了如下两个便捷方法:

1. `$router.back()`后退一层
2. `$router.forward()`前进一层

**在行内使用编程式导航跳转的时候,`this`必须要省略，否则会报错!**

### 导航守卫

导航守卫可以控制路由的访问权限。示意图如下:

![image-20221128123448389](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2-4/image-20221128123448389.png)

**全局前置守卫**

每次发生路由的导航跳转时，都会触发全局前置守卫。因此在全局前置守卫中，程序员可以对每个路由进行访问权限的控制:

![image-20221128132040472](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2-4/image-20221128132040472.png)

```js
// 只要发生了路由的跳转，必然会触发beforeEach指定的 function回调函数
// to 是将要访问的路由的信息对象
// from是将要离开的路由的信息对象
// next 是一个函数，调用next()表示放行，允许这次路由导航
router.beforeEach(function(to, from, next) {
    // next()函数表示放行的意思
    next()
})
```

**next函数的3种调用方式**

![image-20221128133552767](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2-4/image-20221128133552767.png)

**控制后台主页的访问权限**

![image-20221128134257115](https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/notes/vue2-4/image-20221128134257115.png)

## Vant组件库

[官网地址](https://vant-contrib.gitee.io/vant-weapp/0.x/#/intro)

[帮助文档](https://youzan.github.io/vant-weapp/#/quickstart)

### 安装

vue2项目使用`npm install vant -S`安装，vue3项目使用`npm install vant@next -S`安装
