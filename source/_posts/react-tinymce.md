---
title: react集成tinymce编辑器
date: 2018.09.05
---

![](http://feng89.b0.upaiyun.com/img/react-tinymce-1.jpg)

<!-- more -->

## 加载，并渲染tinymce

由于tinymce体积较大，在项目中编辑器如果不是常用的，就不需要打开网页就加载tinymce。

比较好的办法是，在要使用的时候，去加载tinymce相关的资源。

所以利用webpack的`import`api实现懒加载。[api详情地址](https://webpack.docschina.org/api/module-methods#import-)

```js
// 在react渲染完dom后，
// 加载tinymce文件
componentDidMount () {
this.loadTinFile()
  .then(() => {
    // 渲染tinymce
    tinymce.init({
      selector: '#tinymceEdit',
    })
  })
}

loadTinFile () {
// 懒加载
return Promise.all([
  import(/* webpackChunkName: "tinymce" */ 'tinymce'),
  import(/* webpackChunkName: "tinymce" */ 'tinymce/skins/lightgray/skin.min.css')
])
  .then(() => {
    return Promise.all([
      // 语言包
      import(/* webpackChunkName: "tinymce" */ './tinymce_zh_CN'),
      // 主题
      import(/* webpackChunkName: "tinymce" */ 'tinymce/themes/modern')
    ])
  })
}

render () {
  return (
    <div className='tinymce'>
      <textarea id='tinymceEdit' />
    </div>
  )
}


```
1. react渲染出`id tinymceEdit`的dom
1. import加载tinymce文件，import api是异步的，会返回Promise
1. `tinymce.init`初始化编辑器

webpackChunkName "tinymce"， 在编译的时候，生成名字为`tinymce.chunk.js`的文件


至此就会渲染tinymce编辑器了。

## 编辑器内容样式

tinymce使用`iframe`渲染出编辑部分，所以外包定义的样式是无效的，需要在iframe内部加载css样式。

在初始化的时候，使用`content_css`参数定义css样式的链接地址，

tinymce在初始化的时候，就会添加`link css`链接

```js
tinymce.init({
  selector: '#tinymceEdit',
  content_css: `${process.env.PUBLIC_URL}/tinymce/content.css`,
})
```

这里css是被当成静态资源使用，所以无法被webpack处理

## 插件

插件使用文档，查看[plugins文档]((https://www.tiny.cloud/docs/plugins/))

插件的使用，需要先加载插件文件，再初始化的时候，配置`plugins`字段，让tinymce加载插件

```js
// 会使用的插件
const tinyPlugins = [
  'code',
  'fullscreen',
  // 处理a链接
  'link',
  // 字体和背景颜色
  'textcolor',
  // 扩展颜色选择，可以有更多的颜色
  // 'colorpicker',
  // 排序
  'lists',
  // 更多的排序选项
  'advlist',
  // 字数统计
  'wordcount',
  // 表格
  'table',
  // 预览
  'preview',
  // 打印
  'print',
  // 插入日期时间
  'insertdatetime',
  // 插入符号
  'charmap',
  // 插入分页符号
  'pagebreak',
  // 媒体插入
  'media'
]

componentDidMount () {
this.loadTinFile()
  .then(() => {
    // 渲染tinymce
    tinymce.init({
      selector: '#tinymceEdit',
      toolbar: [
        'code | undo redo | bold italic underline strikethrough subscript superscript removeformat blockquote | forecolor backcolor h2 p kityformula | numlist bullist | fullscreen',
        ' fontsizeselect fontselect | alignleft aligncenter alignright alignjustify | outdent indent | link unlink openlink | upimg',
        'pagebreak insertdatetime charmap | table tabledelete tableinsertrowbefore tableinsertrowafter tabledeleterow tablemergecells tablesplitcells | print preview | media'
      ],
      plugins: tinyPlugins.join(',')
    })
  })
}

loadTinFile () {
  return Promise.all([
    // 插件
    ...tinyPlugins.map((item) => {
      return import(/* webpackChunkName: "tinymce", webpackMode: "lazy-once" */ `tinymce/plugins/${item}`)
    })
  ])
}
```
这里使用的插件，都是使用在`toolbar`里面，如果未加载和定义插件，toolbar里面的按钮就不会显示

待续···

## 初始文本

> Failed prop type: You provided a `value` prop to a form field without an `onChange` handler. This will render a read-only field. If the field should be mutable use `defaultValue`. Otherwise, set either `onChange` or `readOnly`.

## 添加按钮

[setup](https://www.tiny.cloud/docs/configure/integration-and-setup/#setup)，在编辑器渲染前，执行的回调

使用`addButton`添加按钮，

[execCommand('mceToggleFormat')](https://www.tiny.cloud/docs/advanced/creating-a-custom-button/#togglebutton)，编辑器内部命令，添加对应的格式

[onPostRender](https://www.tiny.cloud/docs/advanced/creating-a-custom-button/#buttonoptions)，当按钮显示后的回调执行，绑定编辑器初始化。

[formatter.formatChanged](https://www.tiny.cloud/docs/api/tinymce/tinymce.formatter/#formatchanged)，在编辑器内，鼠标选择的部分和参数相同的时候回调，让按钮突出显示

## 公式编辑器

## 图片上传