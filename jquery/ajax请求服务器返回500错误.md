在项目开发中，遇到了一个使用jquery中ajax请求时，服务器返回500错误；经过测试，发现是发送数据的类型导致的，

调试过程：
-
先以正常方式请求服务端内容：
```
$.ajax({
	url: '',
	type: 'POST'
	data: {name: 'zhangsan'},
	success: function(){},
	error: function(err){}
})
```
这个时候控制台报错为： 415: unsupport media type， 由此可以看出服务器不支持jquery默认的数据传输类型，那么如何去知道服务器要求的参数类型是什么呢？



由于问题出在项目的单页面上，主项目是由angular开发的，在angular中所有的$http服务返回数据都是正常的，就单独把两个请求的请求体拿出来对比一下，发现angular请求体的类型是字符串json形式，**不要被控制台中Request Payload下面的数据形式误解，它右边有一个view source，点击一下，看他的源码才是真正发出去的数据类型** 也就是`{"name":"zhangsan","age"":"16"}`的形式。

而jquery请求体中的数据类型是`name=zhangsan%age=16`的形式，这是jquery默认的传输参数类型，如果你的data字段内是一个json对象字面量，jquery会在传递参数的时候，自动把data内容转变成`'name=zhangsan%age=16'`的形式。这样以来就知道怎么改了 ：

1. 制定ajax请求的`Content-Type`属性为`text/plain`,

2. 用`JSON.stringify(data)`方法，把对象类型的参数，转成字符串类型的json对象

至此，问题解决







