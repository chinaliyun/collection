CommonsChunkPlugin插件主要用于建立一个独立的打包文件，这个文件包含多个入口文件的公共模块。可以更有效的利用浏览器并发的特性，不至于把所有的加载压力都放在一个入口文件中，也可以更快速的加载页面

这个插件是webpack内置插件，不需要单独安装

特别注意
=
可能以后来看文档的时候，没有功夫往下看完，因此把这个写在最前面：  当你使用了这个插件分离打包了公共模块，在页面中实际引用的时候，必须按照先引入特殊chunk文件，再引入公共chunk文件，最后引入其他bundle文件的顺序，否则将不能按照预期效果执行

**最好是搭配htmlWebpaclPlugin插件使用更简单**
以本文的案例2为例，引入顺序如下：

```js
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
</head>
<body>
  <!-- 引入特殊chunk信息文件 -->
  <script type="text/javascript" src="output/load.common.js"></script> 
  <!-- 引入公共模块文件 -->
  <script type="text/javascript" src="output/jquery.common.js"></script>
  <script type="text/javascript" src="output/react.common.js"></script>
  <!-- 引入其他文件 -->
  <script type="text/javascript" src="output/manidest.index.js"></script> 
</body>
</html>
```

正文
=
用法
-
```js
new webpack.optimize.CommonsChunkPlugin(option)；
或者
var CommonsChunkPlugin = require('webpack/lib/optimize/CommonsChunkPlugin');
new CommonsChunkPlugin(option);
```

option
-
name: string | names: [ ] 

> 指定公共的chunk name， 可以传递一个字符串，也可以传入一个内含chunk name的数组，如果传入了一个数组，webpack会根据数组内容和entry的组合，分别打包公共的模块，详见下面的 [案例1]

> 通常来说，当传入一个数组的时候，公共模块会被打包进传入的第一个入口文件中； `特殊chunk代码`的函数，会被大打包进最后一个指定的common chunk文件中， 当只指定了一个公共模块文件的时候，公共模块和`特殊chunk代码`函数会全部会被打包进这一个指定的文件中， 详见下面的[案例2]()， 

filename: string
> 指定公共模块被打包出来的文件模板名称，可以使用[name]关键字，**注意：如果name有对应的entry名称，这个[name]就是entry的名称，否则，指的是name中指定的名称** ， 详见下面的[案例1]()

chunks： string | [ ]
> 只有被这个参数指定的入口文件中的公共模块，才会被打包进指定的公共模块文件

minChunks: numer | Infinity | function(module, count)->boolean
> 这个参数主要是规定公共模块必须在指定数量的入口文件中同时被使用，才能被允许打包为公共模块文件。 比如3的意思就是说，一个模块必须在三个入口文件中同时被引入，才可以被视为公共模块，被打包到公共模块文件中；  该参数最小不能低于2。

> 当参数为一个function的时候，可以用来更为细致的决定模块被打包到哪里，见[案例3]()

children : boolean
> 当chidren为true的时候，name和names属性都会失效，并且会把所有的公共模块打包到每一个入口文件生成的chunk文件中。然而我并没有发现这个参数有P的作用，反而是增大了每一个入口chunk文件的大小

async
> 这个属性，我实在是没有找到有什么作用，他和children结合生成的结果，和完全不使用这个插件的结果都一样， 如果有理解这个属性的同学麻烦告诉我一声

案例1： names属性传入多个值
-
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
上面的这个配置会最终会打包出来5个js文件，分别是：
- 所有文件中的jquery模块，会被打包进jquery.common.js文件
- 所有文件中的react模块，会被打包进react.common.js文件
- index1和index2中其他的公共模块会被打包进manifest.common.js文件
- index1和index2中的其他模块会被继续留在index1.bundle.js和index2.bundle.js中

案例2： 特殊的chunk函数
-
webpack在打包的时候，会记录下一些特殊的内容，比如打包的时间以及打包的map信息等等，我们可以把这些信息单独放进一个文件中去
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
      names: ['manifest', 'jquery','react', 'load'],
      filename: '[name].common.js'
    })
  ]
}
```
以上配置，除了会打包与案例1中类似的5个文件之外，特殊的chunk信息会被打包进load.common.js文件中,需要特别注意的是：

案例3： 传递一个函数参数给minChunks
-
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
      names: ['manifest', 'jquery','react', 'load'],
      filename: '[name].common.js',
      minChunks: function(module, count){
          return module.resource && /react/.test(module.resource) && count > 3
      }
    })
  ]
}
```
这个案例就指定了能被打包进公共chunk文件的两个条件：
1. 模块是一个路径，且该路径包含'react'
2. 该模块在被超过3个的入口文件共同使用
那么就把这个模块放入manifest.common.js文件中













