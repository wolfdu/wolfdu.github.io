---
layout:     post
title:      "Add Sublime to $PATH in Ubuntu"
subtitle:   "Ubuntu环境变量配置"
date:       2017-11-12 23:39:00
author:     "Wolfdu"
header-img: "img/in-post/notes/notes-home.jpg"
catalog:    true
tags:
    - Webpack
---

> 基于手工搭建的[blog](https://github.com/wolfdu/blog)项目中的webpack配置信息详细解读其中配置信息

# Webpack 实战详解

在搭建blog过程中使用到了webpack来对项目中的前端代码进行管理和打包，但是对其中一些配置理解的还不是很清晰，
所以一不做二不休，整体解析一下[blog中的webpack配置](https://github.com/wolfdu/blog/blob/master/admin/build/webpack.base.conf.js)，加深学习。

关于webpack的作用我就不再赘述可以参见[官网介绍](https://doc.webpack-china.org/concepts/),本文中webpack版本为2.x

## Entry

*入口起点（entry point）*：是webpack建立应用程序*依赖关系图（dependency graph）*的起点。
入口起点告诉webpack从哪里开始，并遵循着依赖关系图表进行打包。
可以将入口起点认为是**根上下文(contextual root)**或者**第一个启动文件(app)**。

    entry: {
        vendor: ['vue', 'vuex', 'vue-router'],
        app: './admin/src/main.js'
    }


