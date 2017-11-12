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

    npm install n -g

    n stable

## mongo

## Nginx

    apt-get install nginx

    server{
            listen 7070;
            server_name localhost;

            location / {
                    root /root/project/blog/admin/dist;
                    #index index.html;
                    try_files $uri $uri/ /index.html;
            }

            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }
    }

gzip

            gzip on;
            gzip_disable "msie6";
            gzip_min_length 1k;


            # gzip_vary on;
            # gzip_proxied any;
            gzip_comp_level 6;
            gzip_buffers 16 8k;
            # gzip_http_version 1.1;
            gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;




