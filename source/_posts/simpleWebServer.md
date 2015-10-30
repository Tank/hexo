title: Simple web server by Nginx + PHP-FPM
date: 2015-10-31 00:11:02
tags:
- nginx
- php-fpm
- aws
categories:
- web server
- nginx
---
# Simple web server
I was making an web page at work recently, the main perpose is to integrate the API I wrote with a web page console. So I will need a web server for my site and do the test. After a quick search on google, I decided to use nginx and php-fpm to implement my server.  
I installed it on an AWS-ec2 with Amazon Linux AMI instance.  
最近工作上剛好在做一個網站，主要功能是要把我之前在寫的cloud messaging API用一個網頁主控台的方式接起來，讓不會使用`command line`指令的使用者也可以來使用我們API的功能。經過一番隨意的搜尋研究之後決定使用 Nginx + PHP-FPM 來實現一個可以跑php的網站伺服器。使用的server是裝在AWS的ec2機器Amazon Linux AMI上面。

## Install Nginx and PHP-FPM
```sh
sudo yum install nginx php php-fpm
```
Completed! the installation was pretty easy, but the configuration was annoying. 

Put your `index.php` and all web files to the default Nginx path:
把你的`ndex.php`跟其他網站檔案放到 Nginx 預設的路徑底下:
```sh
/usr/share/nginx/html/index.php
```

## Configure Nginx
Nginx Configuration:
順利安裝完之後開始修改 Nginx Server 設定檔，如下：
<!-- more -->
```sh
sudo vim /etc/nginx/nginx.conf
```
```
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log;
pid        /var/run/nginx.pid;


events {
    worker_connections 1024;
}

http {
    include         /etc/nginx/mime.types;
    default_type    application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;
    include /etc/nginx/conf.d/*.conf;

    index   index.html index.htm;

    server {
        listen       80;
        server_name  localhost;
        root         /usr/share/nginx/html;
        location / {
        index index.php;
        }

        # redirect server error pages to the static page /40x.html
        #
        error_page 404 /404.html;
        location = /40x.html {
        }

        # redirect server error pages to the static page /50x.html
        #
        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        location ~ \.php$ {
            #root           html;
            #root          /usr/share/nginx/html;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            include        fastcgi_params;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            #include        fastcgi_params;
        }
    }
}
```

The only changes I made in nginx.conf after the installation were :
> ```
34: root 	/usr/share/nginx/html;
```
> \# the default root path, you can change it to where you put your files.
> \# Nginx Server的預設路徑，你可以把它改成你自己放檔案的地方。

---
> ```
36: index 	index.php;
```
> \# set index lookup file name
> \# 預設首頁檔名

---
> ```
    53: location ~ \.php$ {
    54:        #root           html;
    55:        #root          /usr/share/nginx/html;
    56:        fastcgi_pass   127.0.0.1:9000;
    57:        fastcgi_index  index.php;
    58:        include        fastcgi_params;
    59:        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    60:        #include        fastcgi_params;
    61:    }
    ```
> \# PHP settings, notice that line:59 `fastcgi_param` is different from the default
> \# PHP 設定，注意59行的`fastcgi_param`跟預設的有點不同。

## Start Nginx and PHP-FPM
start PHP-FPM:
```sh
sudo service php-fpm start
```
---
start Nginx:
```sh
sudo service nginx start
```

## Congratulation!
Now the PHP and web server are all ready to go, open an browser and connect to your server throught your public ip, and you should be able to see the Nginx Welcome page as follow or the index.php made by yourself.
現在全部設定都弄好了，你只要透過AWS給你的public ip 連到你的Server，你就可以看到下面的Nginx預設歡迎畫面，或是你自定的首頁index.php啦!
![Nginx Welcome Page](/uploads/NginxWelcomePage.png)
