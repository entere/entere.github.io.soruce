---
title: docker部署nginx
date: 2019-08-09 23:00:35
tags: [docker]
---


# 在宿主机器创建以下目录

```bash
mkdir -p /var/www/docker/nginx/log    # nginx 日志
mkdir -p /var/www/docker/nginx/conf   # nginx 配置文件
mkdir -p /var/www/docker/nginx/html   # 项目目录

```

<!-- more -->

# 在宿主机器创建Nginx配置文件

vi /var/www/docker/nginx/conf/default.conf

```bash
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}

```


# 在宿主机器创建项目文件

vi /var/www/docker/nginx/html/index.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>Docker</title>
</head>
<body>
<h1>Hello Docker</h1>
</body>
</html>

```



# 运行容器，挂载宿主机目录

```bash
docker container run --name my-nginx -itd -p 8080:80 --privileged=true
-v /var/www/docker/nginx/log:/var/log/nginx
-v /var/www/docker/nginx/conf:/etc/nginx/conf.d
-v /var/www/docker/nginx/html:/usr/share/nginx/html
nginx

#第一个-v:挂载日志目录
#第二个-v:挂载配置目录
#第三个-v:挂载项目目录

```

即

```bash
docker container run --name my-nginx -itd -p 8080:80 --privileged=true -v /var/www/docker/nginx/log:/var/log/nginx -v /var/www/docker/nginx/conf:/etc/nginx/conf.d -v /var/www/docker/nginx/html:/usr/share/nginx/html nginx

```

如果nginx配置文件有变动，需进入容器重启nginx

```bash
docker exec -it my-nginx /bin/bash
```


# 查看网站

localhost:8080




