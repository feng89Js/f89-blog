---
title: 小程序中使用lodash
date: 2018.09.17
categories:
  - 开发笔记
tags:
  - 小程序
  - lodash
---

`lodash`是一个非常强大的库，在日常开发中经常会用到。

但他的体积接近`70kb`，而小程序要求，源码等文件，一共不得超过2M。

<!-- more -->

lodash中也有很多方法用不到，如果项目有使用webpack，就可以使用[lodash-webpack-plugin](https://github.com/lodash/lodash-webpack-plugin)和[babel-plugin-lodash](https://github.com/lodash/babel-plugin-lodash)，按需加载lodash。

## 在小程序中，按需使用

但小程序如果要用webpack，很常不方便。

其实，lodash自己就可以按需打包成一个js，从而减少体积。[文档](https://lodash.com/custom-builds)

全局安装lodash，

```
npm i -g lodash-cli
```

比如我们需要使用lodash中的 `get`,`isEmpty`,`last`方法

```
lodash include=get,isEmpty,last
```

就会生成`lodash.custom.js`文件，他的压缩版只有不到8k的大小

当然，你要用的方法越多，文件越大。

把生成的`lodash.custom.js`，放到小程序中就可以使用了。

如果想放在`app`这样全局的变量中，需要用`解构运算`，例如

```js
//app.js
import lodash from './utils/lodash.custom.js';
App({
  $_: {...lodash},
})
```