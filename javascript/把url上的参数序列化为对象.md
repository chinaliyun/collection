代码：
-
```js
function serializeUrlParams(){
	var obj = {};
	var href = location.href;
	if (href.indexOf('?') > -1) {
	    var params = href.slice(href.indexOf('?') + 1);
	    params.split('&').forEach(function(item) {
	        item.split('=')
	        obj[item.split('=')[0]] = item.split('=')[1]
	    })
	}
	return obj;
}
```
有一次在获取参数的时候，出现了错误，问题在于出现了类似这样的url
`http:/baidu.com/index.html?name=zhangsan&age=14=23`

其中age的参数值也包含=号，因此上面的方法已经不管用了，更改后的方法如下

```
function serializeUrlParams(url){
	var obj = {};
	if (url.indexOf('?') > -1) {
	    var params = url.slice(url.indexOf('?') + 1);
	    params.split('&').forEach(function(item) {
	        item.split('=')
	        var index = item.indexOf('=');
	        obj[item.slice(0,index)] = item.slice(index+1)
	    })
	}
	return obj;
}
```
