---
layout:     post
title:      "Add Sublime to $PATH in Ubuntu"
subtitle:   "Ubuntu环境变量配置"
date:       2017-11-12 23:39:00
author:     "Wolfdu"
header-img: "img/in-post/notes/notes-home.jpg"
catalog:    true
tags:
    - Ubuntu
---

## git

    apt-get install git

    git --version

## node

    apt install nodejs

    apt install nodejs-legacy

    apt install npm

    npm install n

    n stable

## mongo

## Nginx

    apt-get install nginx

    server{
            listen 80;
            server_name localhost;

            location / {
                    alias /home/ubuntu/project/blog/admin;
                    #index index.html;
                    try_files $uri $uri/ /index.html;
            }

            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }
    }


