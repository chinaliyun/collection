优化输出结果
=
css压缩
-
css-loader 在webpack2里默认是没有开启压缩的，最后生成的css文件里有很多空格和tab，通过配置 css-loader?minimize 参数可以开启压缩输出最小的css。css的压缩实际是是通过 cssnano 实现的

JS压缩
-
webpack --optimize-minimize 选项会开启 UglifyJsPlugin来压缩输出的js，但是默认的UglifyJsPlugin配置并没有把代码压缩到最小输出的js里还是有注释和空格，需要覆盖默认的配置：
```js
new webpack.optimize.UglifyJsPlugin({
    // 最紧凑的输出
    beautify: false,
    // 删除所有的注释
    comments: false,
    compress: {
      // 在UglifyJs删除没有用到的代码时不输出警告
      warnings: false,
      // 删除所有的 `console` 语句
      // 还可以兼容ie浏览器
      drop_console: true,
      // 内嵌定义了但是只用到一次的变量
      collapse_vars: true,
      // 提取出出现多次但是没有定义成变量去引用的静态值
      reduce_vars: true,
    }
})
```
css单独打包
-
把样式打包到单独的bundle中，与应用程序分离，加强了样式的可缓存性，并且浏览器能够并行加载应用程序代码中的样式文件，避免无样式内容造成的闪烁问题(FOUC - flash of unstyled content)。用法如下(更详细的看[ExtracTextPlugin插件解读]('./ExtracTextPlugin插件解读')):

- 安装包
```js
npm i --save-dev extract-text-webpack-plugin@beta
```
- 引入和使用
```js
var ExtractTextPlugin = require('extract-text-webpack-plugin');

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
分离公共模块
-
当存在多个入口文件的时候，把他们公用的入口文件分离出来，成为一个单独的入口文件，也会有助于浏览器的加载，以及减少单个入口文件的大小
更加详细用法请看[CommonsChunkPlugin插件]()文档
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
    new webpack.optimize.CommonsChunkPlugin({
      names: ['manifest', 'jquery','react'],
      filename: '[name].common.js'
    })
  ]
}
```

提高打包速度·resolve相关
=
配置模块库
-
resolve.modules 配置模块库（通常是指node_modules）所在的位置，在js里出现 import 'redux' 这样不是相对也不是绝对路径的写法时会去node_modules目录下找。但是默认的配置会采用向上递归搜索的方式去寻找node_modules，但通常项目目录里只有一个node_modules在项目根目录，为了减少搜索我们直接写明node_modules的全路径：
```js
module.exports = {
    resolve: {
    modules: [path.resolve(__dirname, 'node_modules')]
  }
};
```

使用alias配置路径映射
-
发布到npm的库大多数都包含两个目录，一个是放着cmd模块化的lib目录，一个是把所有文件合成一个文件的dist目录，多数的入口文件是指向lib里面下的。 默认情况下webpack会去读lib目录下的入口文件再去递归加载其它依赖的文件这个过程很耗时，alias配置可以让webpack直接使用dist目录的整体文件减少文件递归解析。配置如下：
```js
module.exports = {
    resolve: {
    alias: {
      'moment': 'moment/min/moment.min.js',
      'react': 'react/dist/react.js',
      'react-dom': 'react-dom/dist/react-dom.js'
    }
  }
};
```

提高打包速度·loader相关
=
缩小loader搜索范围
-
- loader的test正则表达式也应该尽可能的简单，比如在你的项目里只有 .js 文件时就不要把test写成 /\.jsx?$/
- 使用include指定需要loader处理的文件范围，比如下面的配置就指定只编译src中的匹配文件
```js
loader: {
  test: /\.js$/,
  loader: ['babel-loader'],
  include: [path.resove(__dirname, 'src')]
}
```

开启babel-loader缓存
-
babel编译过程很耗时，好在babel-loader提供缓存编译结果选项，在重启webpack时不需要创新编译而是复用缓存结果减少编译流程。babel-loader缓存机制默认是关闭的，打开的配置如下
```js
loaders: [
        {
        test: /\.js$/,
          loader: 'babel-loader?cacheDirectory',
        }
    ]
```

提高打包速度·module相关
=
使用noParse配置不需要打包的文件
-
有些库是自成一体不依赖其他库的没有使用模块化的，比如jquey、momentjs、chart.js，要使用它们必须整体全部引入。 webpack是模块化打包工具完全没有必要去解析这些文件的依赖，因为它们都不依赖其它文件体积也很庞大，要忽略它们配置如下：
```js
module.exports = {
    module: {
      noParse: /node_modules\/(jquey|moment|chart\.js)/
    }
};
```








