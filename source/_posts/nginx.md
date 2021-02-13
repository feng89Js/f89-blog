---
title: nginx笔记--centos 7 安装
date: 2018.05.17
updated: 2018.09.19
categories:
  - 工具
tags:
  - nginx
  - 跨域代理
---

如果项目是`前后端分离`，前端去访问后端提供的接口，而这时后端没有提供跨域。

浏览器为了安全，也不能跨域访问。就需要借助到有能力发起请求的工具，

其中，最常见的就是`Apache`，和这次的介绍的`nginx`，

## 介绍

> Nginx（发音同engine x）是一个异步框架的 Web服务器，也可以用作反向代理，负载平衡器 和 HTTP缓存。

我们主要使用`web服务器`和`反向代理`

<!-- more -->

## 安装

首先得有一台vps（虚拟服务器），这里系统为 `centos 7`，

这次介绍`编译安装`，好处是根据当前系统，得到`最优的安装`，还可以进行一定的`自定义设置`

### 运行库

检查运行库是否安装完整，否则会安装失败，

像新开的vps，运行库可能会没有，也可能版本较低，我们就要检测更新下。

```bash
rpm -q pcre pcre-devel zlib zlib-devel openssl openssl--devel gcc-c++
```

![](http://feng89.b0.upaiyun.com/blog/截图_1537285788153.png)

不过一般新开的vps，就直接安装运行库，

安装运行库

```bash
yum install -y pcre pcre-devel zlib zlib-devel openssl openssl--devel gcc-c++
```

使用`-y`参数，就会自动安装


### 安装nginx

在[nginx](http://nginx.org/en/download.html)里面找到最新版，复制扩展名为`tar.gz`下载链接，这个为Linux的源码安装包

```bash

# 检查是否安装了nginx，什么都没有提示，表示没有安装
find -name nginx

# 下载，粘贴最新的下载包
wget http://nginx.org/download/nginx-1.13.9.tar.gz
# 解压下载包
tar zxvf nginx-1.13.9.tar.gz

# 进入解压后的文件夹
cd nginx-1.13.9

# 编译安装前的配置，
# 比如安装指定目录
./configure --prefix=/home/nginx

# 编译并安装
make&&make install

## 进入安装nginx里的sbin目录
cd /home/nginx/sbin
# 启动
./nginx
# 重启
# ./nginx -s reload
```

到这里，nginx就安装并启动了，

默认会使用nginx目录下的html文件夹里面的静态文件，并使用`80`端口

范围你vps的ip即可访问，

如果访问不到，可能是防火墙没有开放端口

## 打开防火墙

这里使用centos7，系统默认自带`firewall`防火墙

```bash
# 查看开放的端口,什么都没有显示,没有端口打开
firewall-cmd --list-ports
# 开启80端口
firewall-cmd --permanent --zone=public --add-port=80/tcp
# 重启防火墙
firewall-cmd --reload
```
