模块
=
module.noPrase
-
当我们需要引入一些非AMD或者CommonJS规范的插件的时候，我们需要指定这些文件不要经过解析，这样可以从来改善解析时间
```js
module.exports = {
  module: {
    noParse: /jquery|backbone/
  }
};
```
插件
=
设置全局模块
-
在一起场景下，我们可以需要在全局下都引入一个固定的模块，但是又不想再每一个js文件中都手动require一遍，这个时候我们可以使用ProvidePlugin来定义一个全局的模块，最常见的是jQuery
```js
plugins: [
   new webpack.ProvidePlugin({
     $: 'jquery',
   })
]
```
这样一来，只要碰到js文件中使用$符号的内容，webpack会自动引入jQuery的模块