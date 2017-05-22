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
=
title: string
-
html中title元素的内容，
```js
plugins: [
	new htmlWebpackPlugin({
		title: '标题'
	})
]
```
filename : string 
-
指定文件的名称，这个之中可以包含路径
html中title元素的内容，
```js
plugins: [
	new htmlWebpackPlugin({
		title: '标题',
		filename: "ouput/index.html"
	})
]
```
template: String
-
设置生成的html文件的模板，这个要去参考这个插件的template内容，模板是html、jade、ejs等任何类型，但是使用的前提是要加载对应的loader，否则是不能被正确解析的
```js
plugins: [
	new htmlWebpackPlugin({
		title: '标题',
		filename: "template/index.jade"
	})
]
```
inject: true| body | head | false
-
- true|body： 默认值 script标签位于html文件的body地步
- head: script标签位于head标签内
- false： 不在html中引入js代码
```js
plugins: [
	new htmlWebpackPlugin({
		title: '标题',
		filename: "template/index.jade",
		inject: true
	})
]
```
cache : boolean
> 当这个值设置为true的时候，只有当它的内容改变的时候，才会重新生成新的html文件

showErrors: boolean
> 当值设置为true的时候，出错的详情会被写入到html文件中， 这个值默认是true

chunks ： string or [ ]
-
chunks 选项的作用主要是针对多入口(entry)文件。当你有多个入口文件的时候，对应就会生成多个编译后的 js 文件。那么 chunks 选项就可以决定是否都使用这些生成的 js 文件。 如果没有指定，则默认引入全部的入口文件

chunks 默认会在生成的 html 文件中引用所有的 js 文件，当然你也可以指定引入哪些特定的文件
```js
var config = {
  entry: {
    index1: 'index1.js',
    index2: 'index2.js',
    jquery: 'jquery',
    react: 'react'
  },
  ouput: {
    path: path.resolve(__dirname, 'output'),
    filename: '[name].bundle.js'
  }
  plugins: [
    new htmlWebpackPlugin({
    	chunks: ["index1", "index2"]
    })
  ]
}
```
执行webpack命令后，html文件中，只自动引入了index1和index2的js文件

excludeChunks： boolean
- 设置不允许自动引入到html文件中的模块，也就是这里的只不会被自动引入到html文件中
```js
var config = {
  entry: {
    index1: 'index1.js',
    index2: 'index2.js',
    jquery: 'jquery',
    react: 'react'
  },
  ouput: {
    path: path.resolve(__dirname, 'output'),
    filename: '[name].bundle.js'
  }
  plugins: [
    new htmlWebpackPlugin({
    	excludeChunks: ["index1"]
    })
  ]
}
```
执行webpack命令后，除了index1的入口文件没有被自动引入html中之外，其他都被引入了

xhtml： boolean
-
当设置为true的时候，会严格按照xhtml的格式生成html文件，比如 `link`标签，会是个自闭和标签

minify： object
-
如果项目中用了minify的插件，可以在这里配置压缩的参数，htmlwebapckplugin内部集成了htmlminify，因此可以通过这个选项配置是否压缩html
```js
plugins: [
	new htmlWebpackPlugin({
		title: '标题',
		filename: "template/index.jade",
		minify: {
		            removeAttributeQuotes: true // 移除属性的引号
		        }
	})
]
```

hash: true| fasle
-
默认值为false， 如果这个值设置为true，在生成的html文件中对script的引用名称后面会增加本次打包的hash值， html中的scipt标签类似于下面的内容：
```html
<script type="text/javascript" src="./js/manifest.common.js?22ba60301ea865295bf7"></script>
<script type="text/javascript" src="./js/main.7c61662b586186af6a2f.js?22ba60301ea865295bf7"></script>
<script type="text/javascript" src="./js/index.88e167996d14efa22acc.js?22ba60301ea865295bf7"></script>
```
注意：
> 在处理浏览器缓存问题上，虽然通过更改这个插件的hash属性也可以解决，但还是推荐在entry文件的chunk名称增加chunkhash值得方式，因为在编译的过程中，如果某个文件没有改动，那么他的chunkhash是不会变的，可以有效利用浏览器的缓存机制，减少请求