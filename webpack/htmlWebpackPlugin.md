在开发过程中，经常会遇到webpack中配置了带有hash，或者chunkhash值得输出文件名，每次更改的时候，都需要去对应的html文件中修改，这无疑是很浪费时间的， htmlWebpackPlugin插件就是为了解决这个麻烦产生的， 他会帮你自动生成一个html文件，并且把下面类型的文件自动引入到html中去：

1. ouput中定义的bundle文件
2. ExtractTextPlugin插件中分离出来的css文件
3. CommonsChunkPlugin插件分离出来的公共模块文件
4. 以及其他插件生成的文件

安装
-
```js
  npm install html-webpack-plugin --save
```
用法
-
```js
var htmlWebpackPlugin = require('html-webpack-plugin');

plugin: [
  new htmlWebpackPlugin(option)
]
```
option
-
title: string
> html中title元素的内容，

filename : string 
> 指定文件的名称，这个之中可以包含路径

template: String
> 设置生成的html文件的模板，这个要去参考这个插件的template内容，模板是html、jade、ejs等任何类型，但是使用的前提是要加载对应的loader，否则是不能被正确解析的



cache : boolean
> 当这个值设置为true的时候，只有当它的内容改变的时候，才会重新生成新的html文件

showErrors: boolean
> 当值设置为true的时候，出错的详情会被写入到html文件中

chunks ： string or [ ]
> 设置允许自动引入到html中的模块，

excludeChunks： boolean
> 设置不允许自动引入到html文件中的模块，也就是这里的只不会被自动引入到html文件中

xhtml： boolean
> 当设置为true的时候，会严格按照xhtml的格式生成html文件，比如 `link`标签，会是个自闭和标签

minify： object
> 如果项目中用了minify的插件，可以在这里配置压缩的参数
