---
layout:     post
title:      "Ubuntu study notes"
subtitle:   " \"Ubuntu下输入法不能输入中文\""
date:       2017-07-01 10:00:00
author:     "Wolfdu"
header-img: "img/in-post/ubuntu/ubuntu-home.jpg"
catalog:    true
tags:
    - Ubuntu
---

> 本文仅为学习笔记使用

在Ubuntu 16.04.2 下使用WebStorm发现输入法不能输入中文字符。

以下是解决方案：

#### 1.首先将键盘输入法系统切换至fcitx

![java-javascript](/img/in-post/ubuntu/input-setting/language-setting.png)

![java-javascript](/img/in-post/ubuntu/input-setting/language-setting-fcitx.png)

#### 2.找到相应软件的shell脚本文件也就是.sh文件，我这里是配置WebStorm，就找到WebStorm下bin/WebStorm.sh。

编辑文件在文件的开头加上如下三行配置：

    export XMODIFIERS=”@im=fcitx”
    export GTK_IM_MODULE=”fcitx”
    export QT_IM_MODULE=”fcitx”

然后重启WebStorm切换输入法试试。

> [*参考文章*](http://blog.csdn.net/asdwkl2584561379/article/details/46375325)