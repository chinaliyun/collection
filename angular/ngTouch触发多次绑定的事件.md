ng-touch在使用的时候，需要安装和引用angular-touch库，$swipe.bind()默认会监听三种事件：touch,mouse,pointer;在项目中一定要记得指定其中一种，否则会触发两次绑定的事件
```js
link: function($scope,ele, attr){
	$swipe.bind(ele,{
		'touchstart': function(e){}...
	}, '在这里指定监控的类型[touch,mouse,pointer]')
}

```