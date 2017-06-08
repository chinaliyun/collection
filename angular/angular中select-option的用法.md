当angular中要使用select-option的时候，标准用法如下，这是一个分页模块中选择每页展示条数的例子：
```
<!-- html -->
<select name="page" id="" class="form-control" ng-model="pageSize" ng-options="x for x in [10,20,30]" ng-change="change()">select>
<span>条数据</span>

<!-- js -->
$scope.pageSize = 10;
$scope.change = function(){
	
}
```
可以实现：

1. 自动遍历出option选项
2. 默认选择与pageSize相同的选项
3. 当改变的时候出发change事件

