## Hexo博客管理项目

### 基本使用方法

常用命令

```bash
$ npm install				# 下载hexo博客管理依赖包

$ hexo clean				# 清除之前生成的静态博客

$ hexo generate				# 生成静态文件博客

$ hexo server				# 启动本地博客服务

$ hexo deploy				# 将静态博客发布到gitee
```

**发布成功后应当更新pages服务**

### 新建文章

如果不指定模板，创建的文章模板为post模板，并且会默认生成在/source/_posts目录中

创建post文章

```bash
# 1、指定创建post文章的目录
$ hexo new --paht <指定目录/文件名> "<文章标题>"
# 例：如下命令表示在/source/_post目录下创建一个名为test/notes的目录
# 文章的文件名为vue2.md，并且会创建一个名为vue2的文件夹用于装此文章的图片资源等等，文章的标题为 Vue2入门笔记
$ hexo new --path test/notes/vue2 "Vue2入门笔记"
```
指定Front-matter，就是每篇文章的基本信息，写在文章md文件的最前面，用---定义作用域。例：

```markdown
---
<!-- 文章标题 -->
title: Hexo建站感言
<!-- 文章封面 -->
cover: https://makun-ing-image-bed.oss-cn-chengdu.aliyuncs.com/hexo-gitee-blog/article/_post/summary/hello-world/cover.jpg
<!-- 是否开启评论 -->
comments: true
<!-- 文章分类，只能有一个 -->
categories:
  - 总结
<!-- 文章标签，可以有多个 -->
tags:
  - 个人博客
  - 聊一聊
<!-- 文章创建时间 -->
date: 2022-11-04 15:41:22
<!-- 文章更新时间 -->
updated: 2022-11-07 13:16:22
<!-- 文章置顶排序，数字越大越靠前 -->
sticky: 100
---
```

### Hexo博客管理相关链接
- [vercel管理](https://vercel.com/makuning/hexo-gitee-blog-waline)
- [vercel代理管理](https://hexo-gitee-blog-waline.19marken.top/ui)
- [waline管理端](https://hexo-gitee-blog-waline-mpolgjsxt-makuning.vercel.app/ui)
- [hexo-theme-butterfly官方](https://butterfly.js.org/)
- [hexo官方](https://hexo.io/zh-cn/)