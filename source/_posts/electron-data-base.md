---
title: electron使用数据库
date: 2018.12.01
categories:
  - 开发
tags:
  - node-sqlite3
  - LevelDB
  - nedb
  - lowdb
---

`electron`开发桌面客户端时，在需要`便携`保存数据，就需要使用到数据库。

一般数据库，较常见的是`mysql`和`MongoDB`，但他们不能在electron上方便使用，并且数据不能便携。

这里介绍6个electron能使用的数据库，并做了一个简单的演示，放在[github](https://github.com/fengbjhqs/electron_dataBase_demo)上，其中node-sqlite3编译了`electron@2.0.14, modules@57`，文件放在项目内。

数据库分别是

* [node-sqlite3](https://github.com/mapbox/node-sqlite3/)
* [LevelDB](https://github.com/Level/levelup)，[leveldown](https://github.com/Level/leveldown)
* [nedb](https://github.com/louischatriot/nedb)
* [lowdb](https://github.com/typicode/lowdb)
* localStorage
* indexedDB

<!-- more -->

其中`node-sqlite3`和`LevelDB`需要编译才能使用。

而且不能使用node编译后的，例如node-sqlite3在[阿里npm](https://npm.taobao.org/mirrors/sqlite3/)上，有编译好的，但无法放在electron上直接使用，需要重新编译

>Electron 同样也支持 Node 原生模块，但由于和官方的 Node 相比使用了不同的 V8 引擎，如果你想编译原生模块，则需要手动设置 Electron 的 headers 的位置。[详情看这里](https://electronjs.org/docs/tutorial/using-native-node-modules)

下面提到的[淘宝镜像](https://npm.taobao.org/mirrors/),，均是这个地址。

## LevelDB

leveldb和`local storage`一样，是`kv`方式保存的

leveldb在github上，有为electron编译后的提供下载，我们就不需要自己编译了。

但需要注意下载的版本，通过`process.versions`查看当然electron的`modules`版本

例如`electron@2.0.14`这个版本，

```js
process.versions
{
  "http_parser": "2.7.0",
  "node": "8.9.3",
  "v8": "6.1.534.41",
  "uv": "1.15.0",
  "zlib": "1.2.11",
  "ares": "1.10.1-DEV",
  "modules": "57",
  "nghttp2": "1.25.0",
  "openssl": "1.0.2n",
  "electron": "2.0.14",
  "chrome": "61.0.3163.100"
}
```

其中`modules@57`，并且当前的系统是`win10 x64位`，就需要下载`leveldown-v4.0.1-electron-v57-win32-x64.tar.gz
`

把下载后的`leveldown.node`，放在`node_modules\leveldown\build\Release\`目录下。

具体使用请查官方文档或者看作者的electron数据库的演示[github](https://github.com/fengbjhqs/electron_dataBase_demo)

## node-sqlite3

sqlite就需要自己编译了，如果leveldown需要编译，方法和这里一样

### 编译环境

* 安装nodejs
* npm需要`全局`安装`node-gyp`, `npm install -g node-gyp`
* 安装`python@2.7`，注意变量环境，和不要安装`python@3`
* 安装Visual Studio Build Tools，[下载地址](http://landinghub.visualstudio.com/visual-cpp-build-tools)，需要勾选`v140工具集`，总共下载`2g`多。

![](http://feng89.b0.upaiyun.com/img/20181130235822.jpg)

最后找一个合适的文件夹下，执行这条命令

`npm install sqlite3 --build-from-source --runtime=electron --target=2.0.14 --dist-url=https://atom.io/download/electron`

* npm install sqlite3 --build-from-source， 编译并安装sqlite3
* --runtime=electron --target=2.0.14 安装`electron@2.0.14`的版本
* --dist-url 编译中需要用的文件，下载地址。默认的地址好像不能用了，也可能是我网络不好，我这无法使用。不要修改为阿里的地址`http://npm.taobao.org/mirrors/atom-shell`，阿里更新不及时。

编译完成后，会把文件生成在`node_modules\sqlite3\build\Release\node_sqlite3.node`，

复制这个文件到electron项目下`node_modules\sqlite3\lib\binding\electron-v2.0-win32-x64\node_sqlite3.node`，其中`electron-v2.0-win32-x64`是因为electron@2.0.14，系统为win10 x64。

## nedb，lowdb

这两个均是`json`文档类型的数据库，使用和查看都很方便。

因为他们会放在内存中，如果存放数据比多的时候，会出现性能问题。

## localStorage，indexedDb

这两都能存放数据使用，但electron默认会把数据文件放在其他位置（win `C:\Users\{系统用户名}\AppData\Roaming\{程序名字}\`），不能达到`便携`的需求。

我们需要修改数据文件位置，放在自定的位置。

通过[app.setPath](https://electronjs.org/docs/api/app#appsetpathname-path)，自定数据目录的位置

在启动electron代码中，添加如下代码。，
```js
...
const appPath = app.getAppPath()

app.setPath('userData', appPath + '/userData')
...
```

修改数据目录在`{当前程序目录}/userData`里面，这样我们就可以达到便携的需求了