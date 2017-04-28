这个问题很坑爹，案例如下：
```
// data.json
{
	name: 'zhangsan',
	age: 14
}
// js
	$http.get('data.json')
		.then(function(res){
			console.log('succ')
		}, function(err){
			console.log('err')
		})
```
执行后，查看控制台中的network，发现这个请求已经有了结果， console中却提示了err，刚开始以为是json的key没有加引号，因为本人的习惯是： 如果字符串内没有变量，就使用单引号，因此就把`data.json`改成了如下形式：
```
// data.json
{
	'name': 'zhangsan',
	'age': 14
}
```
结果发现还是不行，抱着试一试的心态，把里面的单引号全部换成双引号，竟然 **解决了！！！**。。

由此可见，json一定要注意格式啊，要点有2:

1.  如果有数组，数组的最后一项不能有逗号
2.  json的key和value，一定要用双引号包裹，单引号是万万不行的，当然了如果你的value值是一个数字，那么只有这个value可以不用引号包裹