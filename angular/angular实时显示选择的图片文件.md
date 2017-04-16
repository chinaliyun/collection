```
//	html
<input type="file" onchange="angular.element(this).scope().fileNameChanged()">

//	js
$socpe.fileNameChanged = function(){
	var form = document.querySelector('form');
	var url = window.URL.createObjectURL(form.coverImg.files[0])
	var coverImg = document.querySelector('.coverImg');
	coverImg.style.backgroundImage = 'url('+url+')';
}
<!-- 或者直接选择input元素 -->
$socpe.fileNameChanged = function(){
	var url = window.URL.createObjectURL(document.querySelector('input[type=file]').files[0])
	var coverImg = document.querySelector('.coverImg');
	coverImg.style.backgroundImage = 'url('+url+')';
	coverImg.style.backgroundSize = 'auto 100%';
}
```
注意：
1. 这里不能使用`ng-change`方法,必须使用固定的`angular.element(this).scope().fn()`的方式
2. 设置为背景图片的时候，一定要注意background-image的值的格式， 一定要是`'url(\'+url+'\')'`
3. 获取文件信息的方法，在IE和其他浏览器之间是不同的
```
//	IE
document.getElementById(id).value

//	chrome Firefox
document.getElementById(id).files[0]
```
可以通过判断浏览器的UA信息来判断使用哪种方式