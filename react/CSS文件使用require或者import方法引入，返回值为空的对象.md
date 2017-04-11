这是因为css-loader的module参数没有设置为true，这个值默认是false，只需要在webpack.config.js中声明即可，比如
```
{
  test: /\.css$/,
  use: [
    {
      loader: 'css-loader',
      options: {
        modules: true,
        localIdentName: '[path][name]__[local]--[hash:base64:5]'
      }
    }
  ]
}
```
简单点也可这样写：
```

{
	test: /\.css$/,
	loader: ['style-loader', 'css-loader?module']
}
```