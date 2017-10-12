---
layout:     post
title:      "Add Sublime to $PATH in Ubuntu"
subtitle:   "Ubuntu环境变量配置"
date:       2017-10-12 23:39:00
author:     "Wolfdu"
header-img: "img/in-post/notes/notes-home.jpg"
catalog:    true
tags:
    - Ubuntu
---

> 刚刚安装了sublime但是每次都要进入文件夹下手动启动，本着能装逼...能命令行启动就不要用鼠标的原则，决定将
Sublime配置到环境变量，以便使用命令行启动。

### 正文

#### 找到Sublime的安装文件夹

可以cd到安装启动文件的文件夹下敲入`pwd`（Print working directory）
获取当前文件夹的路径比如"/root/Downloads/Sublime Text 3"。

#### 读取并添加环境变量：

    gedit /etc/enviroment

内容大概为这种：

PATH="/usr/local/sbin:/sbin:/bin:/usr/games:/usr/local/games

> 注意这里的路径使用`:`来分隔的

添加Sublime的路径：

PATH="/usr/local/sbin:/sbin:/bin:/usr/games:/usr/local/games:/root/Downloads/Sublime Text 3

保存后重启。

    sudo reboot

> 有可能你打开环境变量文件是只读的那么使用`sudo gedit /etc/enviroment`

重启后刷新环境变量并查看是否更新：

    echo $PATH && gedit /etc/enviroment

由于我们想通过直接输入sublime来运行，只需要将运行文件sublime_text拷贝一份改名为sublime即可

    cp sublime_text sublime

在命令行试试输入sublime，搞定。