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
dds
dfsfs
ds

