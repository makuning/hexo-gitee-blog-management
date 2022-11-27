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
updated: 2022-11-27 15:04:46
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



































