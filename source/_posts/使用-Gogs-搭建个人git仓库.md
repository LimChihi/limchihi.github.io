---
title: 搭建个人git仓库
date: 2017-04-10 13:33:17
tags: 
- Ubuntu
- Linux
categories: 运维
toc: true
comments: false
description: 使用gogs搭建私人git仓库，并使用nginx反代理
---

## 前言

因为有些仓库上传到Github并不是非常合适，所以就搭建了一个自己的私人仓库。

在安装Gogs前，我也尝试了Gitlab，但Gitlab直接把我的1g小内存给吃光了，所以便换了Gogs，后来发现只吃了100m的内存，效果很不错

## 环境

- Ubuntu Server 14.04.1 LTS 64
- 腾讯云乞丐版服务器 1核 1GB 1Mbps

## 安装

### 配置Gogs所需的环境

安装nginx

```bash
sudo apt-get install nginx
```



安装git

```bash
sudo apt-get install git
```

安装MySQL

``` bash
sudo apt-get install mysql-server
```

进入数据库

```bash
mysql -u root -p
```

创建gogs数据库

```mysql
SET GLOBAL storage_engine = 'InnoDB';
CREATE DATABASE gogs CHARACTER SET utf8 COLLATE utf8_bin;
GRANT ALL PRIVILEGES ON gogs.* TO ‘root’@‘localhost’ IDENTIFIED BY 'YourPassword';
FLUSH PRIVILEGES;
QUIT；
```

### 安装Gogs

为Gogs创建单独的用户

```bash
sudo adduser git
....
```

cd到根目录，下载Gogs

[Gogs 官方下载页](https://gogs.io/docs/installation/install_from_binary)

```bash
su git
cd ~
wget https://dl.gogs.io/0.11.4/linux_amd64.zip
unzip linux_amd64.zip
```

## 配置Gogs

### 配置Service命令


修改Gogs  service配置文件

```bash
vim /home/git/gogs/scripts/init/debian/gogs
```

```bash
PATH=/sbin:/usr/sbin:/bin:/usr/bin
DESC="Go Git Service"
NAME=gogs
SERVICEVERBOSE=yes
PIDFILE=/var/run/$NAME.pid
SCRIPTNAME=/etc/init.d/$NAME
WORKINGDIR=/home/git/gogs #这个根据自己的目录修改
DAEMON=$WORKINGDIR/$NAME
DAEMON_ARGS="web"
USER=git  #如果运行gogs不是用的这个用户，修改对应用户
```

切会`root`账户然后复制到`/etc/init.d/`

```bash
sudo cp /home/git/gogs/scripts/init/debian/gogs /etc/init.d/
```

增加执行权限

```bash
sudo chmod +x /etc/init.d/gogs
```

### 启动Gogs

```bash
sudo service gogs start
```

在自己浏览器上配置Gogs, `localhost`替换成自己的ip地址

`http://localhost:3000/install`

有关Gogs的配置文件在`/home/git/gogs/custom/conf/app.ini`里面，相关配置在[Gogs文档](https://gogs.io/docs/advanced/configuration_cheat_sheet)中有。

### nginx 反代理

现在访问Gogs都需要在域名后面加入3000的端口号，可以设置nginx反代理，通过二级域名跳转到指定端口

创建相应的配置文件

```bash
sudo vim /etc/nginx/sites-enabled/gogs.conf
```

添加

```bash
server {
        listen 80;
        server_name  code.limchihi.cn;
        location / {
                proxy_pass http://127.0.0.1:3000/;
        }
}
```

Done