---
title: Nginx通过二级目录映射不同的反向代理
date: 2017-08-17 22:03:54
tags: [Nginx,代理]
---

# 需求

同一个域名需要反向代理到不同的服务器，比如:
用户访问 xxx.com/index 需要代理到10.10.4.100和10.10.4.101这两台服务器上 
用户访问 xxx.com/admin 需要代理到10.10.4.200和10.10.4.201这两台服务器上

<!-- more  -->

# 实现

Nginx 主要的作用就是反向代理和负载均衡了

```bash

server {
    listen 80;
    server_name xxx.com;
    #通过访问index二级目录来访问网站前台
    location /index/ {
            #Index后面的斜杠是一个关键，没有斜杠的话就会传递service到后端节点导致404
            proxy_pass      http://Index/;
            proxy_redirect  off;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    #通过访问admin二级目录来访问网站后台
    location /admin/ {
        proxy_pass http://Admin/;
        proxy_redirect  off;
        proxy_set_header  Host  $host;
        proxy_set_header  X-Real-IP  $remote_addr;
        proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
 
#简单的负载均衡节点配置
upstream Index {
     server 10.10.4.100;
     server 10.10.4.101;
     ip_hash;
     
 }
upstream Admin {
     server 10.10.4.200;
     server 10.10.4.201;
     ip_hash;
     
}

```

