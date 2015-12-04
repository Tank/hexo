title: Nginx + PHP web server on docker.
date: 2015-12-04 16:14:52
updated: 2015-12-04 16:14:52
tags:
- docker
- nginx
- php
categories:
- web server
- nginx
- docker
---
# Deploy a Nginx + PHP environment on Amazon EC2 using docker
---
## 簡介 Docker (What is Docker?)
[Docker](http://www.docker.com/) 是一個開源專案，支援多平台，從筆電到公、私有雲上能進行快速部署輕量、獨立的作業環境。
[Docker](http://www.docker.com/) is an open platform for distributed applications for developers and sysadmins.
![Docker img](/uploads/large_v.png "Docker")
## 目的 (Content)
主要介紹如何用 Docker 在 AWS 上部署一個 nginx + php 的環境，會提到以下幾個部分：

- 在 Amazon 的 EC2 上安裝 Docker
-  註冊及登入 Docker Hub 帳號
- 下載 docker image 來部署環境
- 完成

Will focus on how to deploy an nginx + php environment using docker on amazon EC2:

- install docker on Amazon EC2
- Register and login to Docker Hub (Optional)
- deploy using docker image
- Done
<!-- more -->	
## 在 Amazon 的 EC2 上安裝 Docker (To install Docker on an Amazon Linux instance)
1.  Connect to your instance. see [Connect to Your Linux Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html). 
連到你的EC2 instance.
2.  Update the installed packages and package cache on your instance.
更新package
	```bash
	[ec2-user ~]$ sudo yum update -y
	```
3.  Install Docker.
安裝Docker
	```bash
	[ec2-user ~]$ sudo yum install -y docker
	```
4. Start the Docker service.
啟用Docker
	```bash
	[ec2-user ~]$ sudo service docker start
	Starting cgconfig service:                           [  OK  ]
	Starting docker:                                     [  OK  ]
	```
5. Add the `ec2-user` to the `docker` group so you can execute Docker commands without using `sudo`.
把`ec2-user`這個使用者加到docker的個group裡面，這樣可以不用每次用docker的時候都要打`sudo`
	```bash
	[ec2-user ~]$ sudo usermod -a -G docker ec2-user
	```
6. Log out and log back in again to pick up the new `docker` group permissions.
登出再登入讓上一步的設定生效。
7. Verify that the `ec2-user` can run Docker commands without `sudo`.
用`docker info`來驗證Docker已安裝成功。
	```bash
	[ec2-user ~]$ docker info
	Containers: 2
	Images: 24
	Storage Driver: devicemapper
	Pool Name: docker-202:1-263460-pool
	Pool Blocksize: 65.54 kB
	Data file: /var/lib/docker/devicemapper/devicemapper/data
	Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
	Data Space Used: 702.3 MB
	Data Space Total: 107.4 GB
	Metadata Space Used: 1.864 MB
	Metadata Space Total: 2.147 GB
	Library Version: 1.02.89-RHEL6 (2014-09-01)
	Execution Driver: native-0.2
	Kernel Version: 3.14.27-25.47.amzn1.x86_64
	Operating System: Amazon Linux AMI 2014.09
	```
## 註冊及登入 Docker Hub 帳號 ( Register and login to Docker Hub )
- Docker Hub 上提供了許多 images 可以使用，先去註冊 Docker Hub 帳號，網址：https://hub.docker.com/account/signup/
There are many images on Docker Hub. First, go to register an account on Docker Hub：https://hub.docker.com/account/signup/

- 接著可以用 `docker login` 來登入 Docker Hub : 
Use `docker login` to login Docker Hub : 
	```bash
	$ sudo docker login
	Username: [ENTER YOUR USERNAME]
	Password: [ENTER YOUR PASSWORD]
	Email: [ENTER YOUR EMAIL]
	Login Succeeded
	```

## 下載 docker image 來部署環境 (pull docker image to deploy)
1. 先把我建立的 image 抓從我的Docker Hub抓下來
First pull the docker image I created from Docker Hub.
	```bash
	$ docker pull tomroy/nginx-php-fpm-vim:80port
	```
	```bash
	80port: Pulling from tomroy/nginx-php-fpm-vim
	2eaf0096818b: Pull complete
	...
	Digest:sha256:eed24a5da74cf411684c796d82900cfa682f89c97504b8372c7489ac124c6e46
	Status: Image is up to date for tomroy/nginx-php-fpm-vim:80port
	```
2. 利用剛剛抓下來的 image 建立一個 container 並執行裡面的 /sbin/my_init
Run a container using the image we just pulled and run the /sbin/my_init script in that image.
	```bash
	$ docker run -d -p 8080:80 --name docker-host tomroy/nginx-php-fpm-vim: /sbin/my_init
	91646c7285a66ad4a2206a6a8b99d1f789661a110ae30faacdea53ea41fc4aa0
	```
	__/sbin/my_init __: 
	```bash
	#!/usr/bin/env bash
	service php5-fpm start
	nginx
	```
3. 用`docekr ps`來檢查 container 是否已經成功啟動
Use `docker ps` to check if the container is  running.
	```bash
# docker ps
CONTAINER ID        IMAGE                             COMMAND             CREATED             STATUS              PORTS                                     NAMES
91646c7285a6        tomroy/nginx-php-fpm-vim:80port   "/sbin/my_init"     About an hour ago   Up About an hour    443/tcp, 8080/tcp, 0.0.0.0:8080->80/tcp   docker-host
	```
## 完成 (Done)
現在可以直接連到你host的8080 port, `http://<docker_host>:8080`就可以看到php default的網頁了。
You can then browse to http://<docker_host>:8080 to view the default install files.
![php-welcome](/uploads/php-welcome.png)