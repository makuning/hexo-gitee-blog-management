---
title: Vue2入门笔记（2）
comments: true
cover: https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/notes/vue2-2/cover.png
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

![选择需要的下载](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221125163616553.png)

选择vue2.x，然后回车

![选择vue2.x](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221125163924064.png)

选择Less，回车

![选择Less](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221125164020628.png)

选择第一项，将babel等插件的配置项，放到自己独立的文件中，回车

![选择独立配置](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221125164240909.png)

输入y保存我们的选项作为预设，回车

![保存预设](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221125164417096.png)

输入为预设取的别名，然后回车

![为预设取别名](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221125164516315.png)

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

### 组件之间的父子关系

组件封装好之后，彼此之间是相互独立的，不存在父子关系

在使用组件时，根据彼此的嵌套关系，形成了父子关系，兄弟关系

### 使用组件的三个步骤

1. 使用import语法导入需要的组件
2. 使用components节点注册组件
3. 以标签的形式使用刚才注册的组件

```vue
<template>
    <div class="box">
        <!-- 以标签形式，使用注册好的组件 -->
        <Left></Left>
    </div>
</template>

<script>
	// 导入需要使用的.vue组件
	import Left from '@/components/left.vue';
    import Right from '@/components/right.vue';
    
    export default {
        components: {
            'Left': Left,
            'Right': Right
        }
    }
</script>
```

visual studio code 路径提示插件`Path Autocomplete`

### 注册全局组件

之前使用components注册的是私有组件，只能在被导入的组件里使用

在vue项目的**main.js**入口文件中，通过`Vue.component()`方法，可以注册全局组件

```js
import Count from '@/components/Count.vue'

// 第一个参数是组件注册名，第二个参数是需要注册的组件
// 组件注册名建议大写开头
Vue.componnent('MyCount',Count)
```

Visual Studio Code自动补齐标签插件`Auto Close Tag`

### 组件里的props

组件的自定义属性，在封装通用组件的时候，合理使用props可以极大提高组件的复用性

封装者通过props可以让使用者在使用组件时传参

props中的数据，可以直接在模板结构中使用，不能直接修改props的值，props中的值是只读的（可写，但是会报错）

```js
export default {
    // props是自定义属性，允许使用者通过自定义属性，为当前组件指定初始值
    // 在使用标签引入时使用属性来传值
    /*
    	<MyCount init="9"></MyCount>
    	传数字
    	<MyCount v-bind:init="9"></MyCount>
    */
    props: ['init']
    
    data() {
        return {
            // 把props的值赋值给变量
            count: this.init
        }
    }
}
```

**props default默认值**

```js
export default {
	// 只能通过对象方式初始化值
    props: {
        // 自定义属性A
        // 自定义属性B
        // 自定义配置C
        init: {
        	// 如果外界使用此组件时，如果没看又传init值，则默认值生效
            default: 0
    	}
    }
}
```

**props  type值类型**

```js
export default {
	// 只能通过对象方式初始化值
    props: {
        // 自定义属性A
        // 自定义属性B
        // 自定义配置C
        init: {
        	// 如果外界使用此组件时，如果没看又传init值，则默认值生效
            default: 0,
            // 如果传递过来的值与type不相符，那么终端会报错
            type: Number
    	}
    }
}
```

**props required必填项**

```js
export default {
	// 只能通过对象方式初始化值
    props: {
        // 自定义属性A
        // 自定义属性B
        // 自定义配置C
        init: {
        	// 如果外界使用此组件时，如果没看又传init值，则默认值生效
            default: 0,
            // 如果传递过来的值与type不相符，那么终端会报错
            type: Number,
            // 必填项校验，如果使用此组件时没有传init的值，会报错
            required: true
    	}
    }
}
```

### 样式冲突

写在.vue组件中的样式会全局生效，会造成多个组件样式冲突的问题。

为组件中的style标签加上`scoped`属性，可以解决这个问题。

**scoped原理：为组件中每个标签添加一个自定义属性，在style中使用属性选择器。**

```vue
<style lang="less" scoped>
    h3 {
        color: red;
    }
</style>
```

如果使用`scoped`属性，那么它的父元素就不能修改它的样式，就需要用到`/deep/`修改子组件的样式

当使用第三方组件库的时候，如果需要更改第三方默认样式，则需要用到`/deep/`

```vue
<style lang="less" scoped>
    /deep/ h5 {
        color: red;
    }
</style>
```

### vue组件的实例对象

浏览器并不能识别.vue文件，是由`vue-template-compiler`包编译成JS交给浏览器来执行。

以标签形式引用组件才会创建组件实例。

## 生命周期

一个组件从创建 -> 运行 -> 销毁的整个阶段，强调的是一个时间段。

![image-20221126104840924](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221126104840924.png)

[vue2官方文档](https://v2.cn.vuejs.org/)

一使用组件的标签就相当于new了一个组件实例

### 组件创建阶段

![image-20221126113123020](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221126113123020.png)

```js
export default {
    data() {
        return {
            message: 'hello',
        }
    },
    // 组件的props/data/methods都尚未被创建，处于不可用状态
    beforeCreate() {
        
    },
    // 组件的props/data/methods都已被创建，但是组件的模板结构尚未生成
    created() {
        concole.log(this.message);
    },
    // 将要把内存中编译好的HTML结构渲染到浏览器中，此时浏览器中还没有当前组件的DOM结构。
    // 几乎不会被用到
    beforMount() {
        
    },
    // 已将把内存中的HTML结构，渲染到了浏览器
    mounted() {
       // 可以在此操作DOM元素 
    },
    // 数据发生了变化，但页面元素还没更新
    beforeUpdate() {
        
    },
    // 已根据最新的数据完成了组件DOM元素的重新渲染
    updated() {
    	// 当数据变化后希望操作最新的DOM结构在此操作
    },
    // 组件将要被销毁时（组件还没有被销毁）
    beforeDestroy() {
        
    }
}
```

**created生命周期函数非常常用，经常在它里面调用methods中的方法，请求服务器的数据，并且将请求到的数据，转存到data中，供template模板渲染使用**

![image-20221126134313003](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221126134313003.png)

![image-20221126134334379](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221126134334379.png)

### 组件运行阶段

![image-20221126135131635](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221126135131635.png)

### 组件销毁阶段

![image-20221126201935215](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221126201935215.png)

## 父子传值

### 父向子使用自定义属性

父类在标签中使用props传值+标签传值，子组件定义自定义属性

```vue
<!--子组件中定义 -->
<script>
	export default {
        props: ['init']
    }
</script>

<!-- 父组件中定义 -->
<template>
	<Son :init="msg"></Son>
</template>

<script>
    export default {
        data() {
            return {
                msg: 'hello'
            }
        }
    }
</script>
```

**不要修改props的值**

### 子向父使用自定义事件

子向父共享数据使用自定义事件

```vue
<!-- 子组件 -->
<script>
	export default {
        data() {
            return {
                count: 0
            }
        },
        methods: {
            add() {
                this.count += 1;
                // 修改数据时，通过$emit()触发自定义事件
                this.$emit('numchange', this.count);
            }
        }
	}
</script>

<!-- 父组件 -->

<template>
	<!-- 绑定自定义事件 -->
	<Son @numchange="getNumCount"></Son>
</template>

<script>
	export default {
        data() {
            return {
                countFromSon: 0
            }
        },
        methods: {
            getNewCount(val) {
                this.countFromSon = val
            }
        }
    }
</script>
```

### 兄弟组件之间的数据共享

Vue2中，兄弟组件之间数据共享的方案是EventBus

这是一种现成的解决方案

![image-20221126211531540](https://gitee.com/markening/image-bed/raw/master/hexo-gitee-blog/article/_post/notes/vue2-2/image-20221126211531540.png)

**使用步骤**

1. 创建eventBus.js模块，并向外共享一个Vue的实例对象
2. 在数据发送方，调用`bus.$emit('事件名称',要发送的数据)`方法触发自定义事件
3. 在数据接收方，调用`bus.$('事件名称',事件处理函数)`方法注册一个自定义事件

## ref引用

在vue中，程序员不需要操作DOM，只需要将数据维护好。（数据驱动视图）

在vue项目中，强烈不建议安装和使用jQuery

ref辅助程序员在不依赖jQuery的情况下，获取DOM元素或组件的引用

每个vue的组件实例上，都包含一个`$refs`对象，里面存储看对应的DOM元素或组件的引用。默认情况下，组件的`$refs`指向一个空对象。

### ref引用DOM

**如何拿到某个DOM元素的引用**

1. 为标签添加`ref`属性，一个组件里属性的值不能重复
2. 使用`$refs`对象获取标签指定的`ref`值

```vue
<template>
	<h5 ref="myTag"></h5>
</template>

<script>
	export default {
        created() {
            console.log(this.$refs.myTag);
        }
    }
</script>
```

### ref应用组件

**ref也可以用在组件的标签引用上，用`$refs`访问后是组件的实例对象，就可以访问它的属性和方法**

```vue
<template>
	<h5 ref="myTag"></h5>
	<MyComp ref="MyComp"></MyComp>
</template>

<script>
    import MyComp from '@/components/MyComp.vue'
    
	export default {
        created() {
            console.log(this.$refs.myTag);
        },
        components: {
            MyComp
        },
        created() {
            console.log(this.$refs.myTag);
        }
    }
</script>
```

### `this.$nextTick(callback)`

**将callback函数在页面重新渲染好后再执行。**

因为有时候页面还没更新就获取`ref`的值，那个时候`ref`还并没有出现，所以需要用到`this.$nextTick(callback)`

```vue
<template>
	<h5 v-if="isShow" ref="myTag"></h5>
	<h2 v-else ref="myTag2"></h2>
	<button @click="doSome">显示h2</button>
</template>

<script>
	export default {
        data() {
            return {
                isShow: true
            }
        }
        methods: {
            doSome() {
                this.isShow = false;
                // 因为数据更新了，但是DOM还没有重新加载，所以要使用$nextTick方法
                this.$nextTick(() => {
                    console.log(this.$refs.myTag)
                })
            }
        }
    }
</script>
```

## 数组中的方法

### some循环

forEach循环一旦开始就无法在中间停止

```js
const arr = ['a','b','c','d','e','f']

arr.some((item, index) => {
    if (item === c) {
        console.log(item)
        // 在找到对应的项之后，可以通过return true来终止循环
        return true
    }
})
```

### every循环

能用来判断数组中每个对象的某个属性，例如判断是否被全选

```js
const arr = [
    {id: 1, name: '香蕉', state: true},
    {id: 2, name: '苹果', state: true},
    {id: 3, name: '梨子', state: true},
    {id: 4, name: '橘子', state: true}
]

// 需求：判断数组中，水果是否被全选了
const result = arr.every(item => item.state)
console.log(result)
```

### reduce基本使用

```js
const arr = [
    {id: 1, name: '香蕉', state: true, price: 10, count: 1},
    {id: 2, name: '苹果', state: false, price: 80, count: 2},
    {id: 3, name: '梨子', state: false, price: 22, count: 2},
    {id: 4, name: '橘子', state: true, price: 24, count: 5}
]

// 需求： 把购物车数组中，已勾选的水果，总价累加起来
/*
arr.filter(item => item.state).reduce((累加的结果，当前循环项) => {
    
},初始值)
*/
let amt

const result = arr.filter(item => item.state).reduce((amt, item) => {
    return amt += item.price * item.count
},0)	

console.log(result)
```

### reduce简化写法

```js
let amt
const result = arr.filter(item => item.state).reduce((amt, item) => amt += item.price * item.count,0)
console.log(result)
```













































