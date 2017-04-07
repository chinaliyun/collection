angular js的强大之处之一就是他的数据双向绑定这一牛B功能，我们会常常用到的两个东西就是ng-bind和针对form的ng-model。但在我们的项目当中会遇到这样的情况，后台返回的数据中带有各种各样的html标签。如：
```
$scope.currentWork.description = “hello,<br><b>今天我们去哪里？</b>”
```
我们用ng-bind-html这样的指令来绑定，结果却不是我们想要的。是这样的
```
hello,<br><span>今天我们去哪里？</span>
```
angular 1.2以前版本解决办法
-

对于 **angular 1.2** 一下的版本我们必须要使用$sce这个服务来解决我们的问题。所谓sce即“Strict Contextual Escaping”的缩写。翻译成中文就是“严格的上下文模式”也可以理解为安全绑定吧。来看看怎么用吧。

```js
// controller code:
Controller code (controller.js):
$http.get('/api/work/get?workId=' + $routeParams.workId).success(function (work) {$scope.currentWork = work;});
```
```html
<!--HTML code:-->
<p> {{currentWork.description}}</p>
```
我们返回的内容中包含一系列的html标记。表现出来的结果就如我们文章开头所说的那样。这时候我们必须告诉它安全绑定。它可以通过使用`$sce.trustAsHtml()`。该方法将值转换为特权所接受并能安全地使用“ng-bind-html”。所以，我们必须在我们的控制器中引入$sce服务
```js
controller('transferWorkStep2', ['$scope','$http','$routeParams','$sce', function ($scope,$http, $routeParams, $sce) {
    $http.get('/api/work/get?workId=' + $routeParams.workId)
    .success(function (work) {
        $scope.currentWork = work;
        $scope.currentWork.description = $sce.trustAsHtml($rootScope.currentWork.description);
    });
```
```html
<!--html code:-->

<p ng-bind-html="currentWork.description"></p>
```
这样结果就完美的呈现在页面上了：

hello

angular 1.2以后版本解决办法
-

咱们还可以这样用，把它封装成一个过滤器就可以在模板上随时调用了
```js
app.filter('to_trusted', ['$sce', function ($sce) {
    return function (text) {
        return $sce.trustAsHtml(text);
    };
}]);
```
```html
<!--html code:-->

<p ng-bind-html="currentWork.description | to_trusted"></p>
```