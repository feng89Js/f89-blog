---
title: vscode使用笔记
date: 2020.02.26
categories:
  - 工具
tags:
  - vscode
  - 便携
  - 下载地址
  - eslint
---

工欲善其事，必先利其器

## 下载

这个地址，下载到指定的版本

```
// 压缩包（推荐）
https://update.code.visualstudio.com/{版本号}/win32-x64-archive/stable
// 安装包
https://update.code.visualstudio.com/{版本号}/win32-x64-user/stable
```

[vscode更新日志](https://code.visualstudio.com/updates)

[下载页](https://code.visualstudio.com/Download)

<!-- more -->

## 便携化

vscode在更新到[1.25](https://code.visualstudio.com/updates/v1_25#_portable-mode)的时候，开始支持[便携化](https://code.visualstudio.com/docs/editor/portable)

`Windows，Linux`下，只需要在vscode文件目录里创建`data`文件夹，重新打开vscode，以后安装的`扩展`,`配置`,`缓存`，就都会在保存到这个目录

可以方便的找到配置文件；也不用担心重装系统后，配置丢失

还可以放在U盘，随时使用，不过，vscode是基于electron，所以扩展基本是nodejs写的，导致依赖的包文件很碎，在复制和压缩的都很慢。

## 推荐扩展

### [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

>eslint，可以规范代码，使代码更易阅读。[中文文档](https://cn.eslint.org/)

vscode配合eslint，可以在编写代码的时候，eslint实时检查，有错误就会在编辑器显示出来

还需要安装eslint的包，不推荐全局安装，可能会污染到全局的npm库；最近typescript的版本也建议将eslit安装到项目内

除了eslint包，还要根据不同的配置文件，安装eslint的扩展包

项目内执行命令`npx eslit --init`，通过一项项选择，就创建出一份eslint的配置文件，

但他的选项不多，对于新手来说也不友好，所以更多时候会直接复制配置文件到项目下，

```js
module.exports = {
  root: true,

  env: {
    node: true
  },
  parser: '@typescript-eslint/parser',

  extends: [
    'standard'
  ],

  plugins: ['@typescript-eslint'],

  rules: {
    'no-console': 1,
    '@typescript-eslint/no-unused-vars': 'error'
  }
}
```

例如这个配置文件，是平时利用nodejs做小工具的时候使用的配置文件。

这里关键部分是**nodejs**开发，使用**typescript**语法，检验规则是**standard**，

所以`env.node = true`，否则nodejs内置方法会检验错误，

使用ts语法，所以需要安装`@typescript-eslint/parser`解析器和`@typescript-eslint`插件，eslint默认解析器并不识别ts语法，而且默认解析器也不能识别较新的js语法，如果不能识别，可以尝试安装`babel-eslint`解析器

[standard](https://standardjs.com/readme-zhcn.html)规则，开箱即用，eslint的规则非常多，如果全部靠自己维护，会比较费神，所以直接使用配置好的规则，[standard配置](https://github.com/standard/eslint-config-standard)

*在vscode中有`standard`扩展直接使用，未尝试*

最后这个配置文件需要安装的eslint扩展包如下，就可以开始使用。

```
@typescript-eslint/eslint-plugin
@typescript-eslint/parser
eslint-config-standard
eslint-plugin-import
eslint-plugin-node
eslint-plugin-promise
eslint-plugin-standard
```

eslint配置文件名，可以是`.eslintrc.js`需要module.exports一个对象，或者`.eslintrc`用json各式

react eslint配置

```js
module.exports = {
  root: true,

  env: {
    node: true
  },

  parserOptions: {
    parser: 'babel-eslint'
  },

  extends: [
    'standard',
    'standard-react'
  ],

  rules: {
    'no-console': 1,
  }
};
```

vue eslint配置

```js
module.exports = {
  root: true,

  env: {
    node: true
  },

  extends: [
    'plugin:vue/essential',
    '@vue/standard'
  ],

  rules: {
    'no-console': process.env.NODE_ENV === 'production' ? 'error' : 'off',
    'no-debugger': process.env.NODE_ENV === 'production' ? 'error' : 'off'
  },

  parserOptions: {
    parser: 'babel-eslint'
  }
}
```

### [中文语言包](https://marketplace.visualstudio.com/items?itemName=MS-CEINTL.vscode-language-pack-zh-hans)

看着比较情切

### [One Dark Pro](https://marketplace.visualstudio.com/items?itemName=zhuangtongfa.Material-theme)

vsocde默认主题比较亮，不是很顺眼

这个应该是移植他的同门师兄弟`atom`的默认主题包

### 各种语法扩展

vscode有很多语言并不认识，就需要安装对应的语法扩展

例如[Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur)等