angular 1.6版本之后的$http服务，不再支持success或者error方法返回的是一个promise对象，可以用then来代替
```js
$http()
	.then()
```
