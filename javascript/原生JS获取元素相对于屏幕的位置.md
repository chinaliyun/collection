JS获取DOM先对于屏幕的位置，可以使用offsetLeft与offsetparent结合的方式获取，因为offsetLeft获取的是相对于父级的位置，要想获取相对于屏幕的位置，可以通过dom.offserParent != null来判断是否继续向上获取，代码如下：
```
function getLeft(e){ 
	var offset=e.offsetLeft; 
	if(e.offsetParent!=null) offset+=getLeft(e.offsetParent); 
	return offset; 
} 
```