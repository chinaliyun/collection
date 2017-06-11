res.send()
-
这个方法我觉得应该是对`rs.write()`和`res.end()`方法的封装，只需要添加要发送给客户端的内容即可

res.cookie(name, value, options)
-
这个方法用来设置发送的cookie,第一个参数为cookie的key，第二个参数为cookie的value值，第三个参数是其他的一些选项

参数|作用      
 ---|---
    domain |cookie在什么域名下有效，类型为String,。默认为网站域名
    expires |cookie过期时间，类型为Date。如果没有设置或者设置为0，那么该cookie只在这个这个session有效，即关闭浏览器后，这个cookie会被浏览器删除。
    httpOnly |只能被web server访问，类型Boolean。
    maxAge |实现expires的功能，设置cookie过期的时间，类型为String，指明从现在开始，多少毫秒以后，cookie到期。
    path |cookie在什么路径下有效，默认为'/'，类型为String
    secure |只能被HTTPS使用，类型Boolean，默认为false
    signed |使用签名，类型Boolean，默认为false。`express会使用req.secret来完成签名，需要cookie-parser配合使用`

注意有一个与cookie有关的中间件：cookie-parser，这个中间件可以获取到页面的所有cookie,并解析成JSON格式，保存在req.cookies中，使用方法如下：
```js
    var cookieParser = require('cookie-parser');
    app.use(cookieParser());
    app.get('/', function(req,res){
        console.log(req,cookies)
        }})
```