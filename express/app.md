app.use()
-
看了一些文档，我把他理解为在对http请求的过滤，官网上的参数内容是`app.use(path,callback)`。

其中path是可以省略的，默认是`/`根目录，意味着所有的请求都会被中间件过滤一遍(也就是这个中间件会执行一遍),

第二个参数就是过滤的方法了，比如我们每当接收到请求的时候，都要确认一下用户的信息之类的操作，

中间件有很多种，比如最常见的

    Body-parser     用于处理JSON，Raw，Text，URL编码的数据、
    multer          用来处理表单提交的内容、
    cookie-parser   用来解析处理cookie的工具。
利用cookie-parser 我们就可以自定义一个验证用户权限的中间件，比如：
```js
    var cookieParser = require('cookie-parser');
    app.use(cookieParser());
    app.use(function(req,res){
        username = req.cookies.fitsr_name;
        uid = req.cookies.uid;
        //验证用户身份
        if(验证通过){
            继续下一步
        }else{
            next('权限不足')
        }
    })
```
app.set()
-

app.get()
-
1.app.get(name)可以用来获取通过app.set()方法定义的name值
```js
    app.set('title','react-demo');
    app.get('title') //react-demo
```
2.app.get(path,callback[,callback...])用来设置路由，path是服务器上的额路径，callback是当路由匹配时要执行的函数，例如
```js
    // 匹配根目录的访问
    app.get('/', function(req,res){
        res.send('hello world');
        })
```
