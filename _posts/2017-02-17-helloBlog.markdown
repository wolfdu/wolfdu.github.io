---
layout:     post
title:      "Welcome to Wolfdu Blog"
subtitle:   " \"Hello World, Hello Blog\""
date:       2017-02-17 16:59:00
author:     "Wolfdu"
header-img: "img/post-bg-2015.jpg"
tags:
    - 生活
---

> “Yeah It's on. ”

##前言

Wolfdu 的 Blog 就这嘛开通了！！！

[刚正面](#build)



2017 年， 我在这个偌大的世界也有了自己的一块自留地。


作为一个程序员， Blog 这种轮子要是挂在大众博客程序上就太没意思了（虽然blog主要是关注内容）。一是觉得大部分 Blog 服务都太丑（颜控），二是觉得不能随便定制不好玩。之前一直都是想自己搭建一个个人网站，但是blog只是其中的一个模块，后来在github上看到别人blog后就想自己也搭建一个玩玩，记录自己学习的历程。

于是就花了一天时间整了这个blog（自己down的模板，简单修改了一下），现在还只是雏形以后再慢慢的去调整。


<p id = "build"></p>
---

## 正文

接下来说说搭建这个博客的心路历程（踩坑过程）。 

在同事的引导下，发现了[Github Pages](https://pages.github.com/) + [Jekyll](http://jekyllrb.com/)快速搭建Blog的技术方案，赶脚自己变的时尚了起来（陶醉脸）。


其优点非常明显：

* **Markdown** 带来的优雅写作体验
* 非常熟悉的 Git workflow ，**Git Commit 即 Blog Post**
* 利用 GitHub Pages 的域名和免费无限空间，不用自己折腾主机
	* 如果需要自定义域名，也只需要简单改改 DNS 加个 CNAME 就好了
* Jekyll 的自定制非常容易，基本就是个模版引擎

感觉怎么就没发现缺点呢？？？
开玩笑！！！慢慢使用就会发现了（其实我也不知道）。

不知道访问速度的问题算不算一个缺点，感觉国内访问应该还是有点略慢。


---

配置的过程中遇到的坑。

先记录一下本次搭建的流程吧，具体哪个地方遇到坑了再挑出来说。

1.首先当然是在github中创建一个基于你的用户名的repository如：useName.github.io的repository。
![java-javascript](/img/in-post/post-js-version/github-pages-URL.jpg)
<small class="img-hint">URL for User Pages</small>
这样你就可以clone到本地随意的修改了。

下面就可以使用jekyll（基于Ruby）搭建个人的blog了。

2.那神马是Jekyll？？？

Jekyll是一个静态网站生产器（static website generator）。
静态网站即只包含HTML，CSS，JavaScript。不依赖于数据库。所以非常适合用来搭建个人的blog。

那么首先我们要在本地部署我们的Jekyll，Jekyll需要有Ruby环境Windows下直接使用集成好的[Ruby Install](http://rubyinstaller.org/)安装即可。安装教程就不在赘述了。安装好后环境中会自带gem(ruby的包管理系统)，使用过node的npm的同学应该肥肠熟悉。

直接在Terminal里输入:
	
	$ gem install jekyll

输入：	$ jekyll -v，验证安装成功。若成功会返回提示版本信息。
如：
	
	jekyll 3.4.0

这里有个坑由于ruby的源被墙了所以在install jekyll之前应先将ruby的源切到http://gems.ruby-china.org/
	
	$ gem sources --remove https://rubygems.org/
	$ gem sources --add http://gems.ruby-china.org/
	$ gem sources -l
	http://gems.ruby-china.org/
	# 确保只有 gems.ruby-china.org

接下来就是创建Jekyll项目

3.如果你想自己折腾，可以在你userName.github.io文件夹下创建jekyll工程：

想要通过jekyll new一个工程需要安装bundler
	
	$ gem install bundler

安装成功后创建jekyll工程：
	
	$ jekyll new myblog

目录下会生成如下结构文件：
![java-javascript](/img/in-post/post-js-version/new-jekyll-doc.png)

_config.yml是配置文件，包含所有的配置信息
具体的配置修改可参见 [jekyllcd](http://jekyllcn.com/)

Jekyll 会为我们提供一篇默认的博客，运行如下命令就可以在本地查看了：
	
	$ cd myblog/
	$ jekyll serve

注意：此时myblog文件夹下会增加_site文件夹，包含了生成网站的所有结构。
源文件夹名头没有下划线的文件夹会被复制到_site文件夹中，如css，js等文件。

4.当然如果你只想要一个模板然后开始写blog，那就可以很容易了。

[github](https://github.com/Huxpro/huxblog-boilerplate) 或者[Jekyll Themes](http://jekyllthemes.org/)上都会提供很多模板供你选择。

你可以下载下来，本地进行修改就可以使用了。


[感谢 huxpro.github.io 提供的模板](https://github.com/Huxpro/huxblog-boilerplate)


## 结语

这个blog只是一个开始，还有很多有趣的东西等着我去探索，希望这个blog里会有更多有趣的东西。


