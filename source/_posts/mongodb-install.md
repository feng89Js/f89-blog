---
title: mongodb安装
date: 2018.11.25
categories:
  - 工具
tags:
  - mongodb
---

## windows

### 下载

[下载地址](https://www.mongodb.com/download-center/community)

![](http://feng89.b0.upaiyun.com/img/20181125201818.jpg)

这里选择社区最新的版本（如图，4.0.4），

选择windows 64位，zip格式

<!-- more -->

### 安装

把下载的压缩包`mongodb-win32-x86_64-2008plus-ssl-4.0.4.zip`，

解压出来，这里放D盘，`D:\mongodb-win32-x86_64-2008plus-ssl-4.0.4\`

新建一个文件夹，用来存放数据文件， `D:\mongodb-dataBase\`

### 启动

命令行进入`D:\mongodb-win32-x86_64-2008plus-ssl-4.0.4\bin\`

执行`mongod --dbpath d:\mongodb-dataBase`即可启动，如下图


![](http://feng89.b0.upaiyun.com/img/20181125224934.jpg)


可以看见，启动的端口是`27017`

使用`Navicat`来连接下数据库

![](http://feng89.b0.upaiyun.com/img/20181125225408.jpg)

随便写一个连接名，

只有一个连接，在连接选择`standalone`

默认数据库没有密码，所以不需要填写用户名和密码，验证选择`none`，

端口是27017，

确认后，左侧双击连接mongodb

![](http://feng89.b0.upaiyun.com/img/20181125230516.jpg)