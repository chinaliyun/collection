angular中对input[type=file]使用ng-change无效，解决办法如下：

```
<!-- html -->
<input onchange="angular.element(this).scope().change()"/>
```
其中angular.element(this)是获取这个元素

.scope()应该是获取当前元素的作用域的实例，然后调用其中的change方法，

change方法可以在controller或者directive中指定，我一般会在change方法中传递当前的这个input节点，类似于这样：
```
<!-- html -->
<input onchange="angular.element(this).scope().change(this)"/>
```
把当前的input节点传递给事件，就可以针对节点来处理一些事件了，

对于input[type=file]类型来说，最常用的处理事件无非就是实时预览获取的图片文件，方法如下：

```
<!-- js -->
var imgUrl = URL.createObjectURL(ele.files[0]);
angular.element(ele).parent('.upload').next('.thumb').css({
    backgroundImage: 'url('+imgUrl+')'
})
```
详细的结束，请看另一篇具体的文章
