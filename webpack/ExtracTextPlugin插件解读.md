安装
-
```js
npm i --save-dev extract-text-webpack-plugin@beta
```

引入
-
```js
var ExtractTextPlugin = require('extract-text-webpack-plugin');
```
最简单的用法
-
```js
module.exports = {
  module: {
    rules: [
      {
        test: /.\.css$/,
        use: ExtractTextPlugin.extract('css-loader')
      }
    ]
  },
  plugins: [
    new ExtractTextPlugin('styles.css'),
  ]
}
```
new ExtractTextlugin(option: filename:string | object)
-
可以生成一个提取的实例，参数可以是指定输出文件件的完整路径，也可以是一个对象，
- 指定输出文件的完整路径
```js
  plugin: [
    new ExtractTextPlugin('src/style')
  ]
```
- 一个对象作为参数，常用的参数：
名称|描述
---|---
option.filename： | 生成的文件夹名，可以包含路径，如果allChunks参数为TRUE的时候，会包含[name].[id].[contenthash]
option.allChunks：| 如果存在多个入口文件，该插件会输出对应的多个打包文件
option.disabe：   | 禁用当前插件

extractTextPlugin.extract(option: string | array | object)方法
-
从一个loader中创建一个新的“提取加载器”，如果只需要处理一个loader，参数可以是string，如果有多个loader存在，参数需要是一个对象

- 只有一个loader时,且没有其他复杂配置时候：
```js
module: {
  use: [{
    test: /\.css/,
    use: extractTextPlugin.extract('css-loader')
  }]
}
```
- 有多个loader，且没有其他复杂配置的时候
```js
module: {
  use: [{
    test: /\.css/,
    use: extractTextPlugin.extract(["css-loader","less-loader"])
  }]
}
```
或者
```js
module: {
  use: [{
    test: /\.css/,
    use: extractTextPlugin.extract({
      use: ["css-loader","less-loader"]
    })
  }]
}
```
- 有多个loader，且需要其他复杂配置的时候
```js
module: {
  use: [{
    test: /\.css/,
    use: extractTextPlugin.extract({
      use: [
        {loader: "css-loader",option: {},
        {loader: "less-loader",option: {}},
      ]
    })
  }]
}
也就是说，当有多个loader的时候，参数内use的值可以是一个字符串、一个数组， 数组中可以是单独的字符串，也可以是一些对象

注意：
-
这个插件必须在module.rules和plugin中同时使用，才能生效

同时使用多个打包规则
-
该插件允许同时使用多个打包规则，例子如下：
```js
const ExtractTextPlugin = require('extract-text-webpack-plugin');

// 多个提取实例
const extractCSS = new ExtractTextPlugin('stylesheets/[name].css');
const extractLESS = new ExtractTextPlugin('stylesheets/[name].less');

module.exports = {
  module: {
    use: [
      {
        test: /\.css$/,
        use: extractCSS.extract([ 'css-loader', 'postcss-loader' ])
      },
      {
        test: /\.html$/i,
        use: extractLESS.extract([ 'css-loader', 'less-loader' ])
      },
    ]
  },
  plugins: [
    extractCSS,
    extractLESS
  ]
};
```









