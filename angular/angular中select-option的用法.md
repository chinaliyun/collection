目的： 在angular中实现select+option的循环与绑定初始值

使用ng-options实现显示列表并且绑定model
-

当数组中的项目为**字符串**的时候

```
// js
$scope.list = ['zhangsan','lisi','wangwu','zhaoliu'];
$scope.selectValue = $scope.list[0];

//html   使用ng-options
<select name="" id="" ng-options="x for x in list" ng-model="selectValue"></select>


//html 使用ng-repeat
<select name="" id="" ng-model="selectValule">
	<option value="" ng-repeat="x in list" ng-bind="x"></option>
</select>
```


当数组中的项目为**对象**的时候

```
// js
$scope.list = [
	{
		"id":"1",
		"name":"zhangsan",
	},{
		"id":"2",
		"name":"lisi",
	}{
		"id":"3",
		"name":"wangwu",
	}{
		"id":"4",
		"name":"zhaoliu",
	}
];

$scope.selectValue = $scope.list[0];

//html
<select name="" id="" ng-options="x.name for x in list" ng-model="selectValue"></select>

//html 使用ng-repeat
<select name="" id="" ng-model="selectValule.id">
	<option value="{{x.id}}" ng-repeat="x in list" ng-bind="x.name"></option>
</select>
```
注意
-
1. ng-options千万不要少了那个`s`
2. for前面的值是option显示的名称，在数组内是对象的时候，要注意取值方法
3. 数组内是对象的时候，ng-model的指向必须是该对象，千万不要因为option里面的值是x.name, 而把ng-model的值也详细到某个对象的id或者name上
4. 本页面内所有的案例都必须遵循：所有的key和value都必须用双引号或者单引号包括，否则ng-model中设置得到默认值，是不会执行的，会在所有的option元素前面添加一个空值的option元素
