严格模式与非严格模式
-
```
<!-- 非严格模式 -->
function foo(){
	console.log(!this)
}
foo(); // false

<!-- 严格模式 -->
'use strict'
function add(){
    console.log(!this)
}
add()   // true


```
严格模式下，禁止this指向全局对象window，这个时候的this指向undefined


使用babel把ES6 转成ES5
-
```
<!-- 编译前 -->
'use strict'
var foo = () => this;

<!-- 编译后 -->
'use strict'
var foo = function foo() {
	return undefined;
};
```

```
<!-- 编译前 -->
'use strict'
var foo = function(){
	return () => this
}
<!-- 编译后 -->
'use strict'
var foo = function(){
	var _this = this;
	return function(){
		return _this;
	}
}
```
ES6中的箭头函数中如果有this，babel在将他转成ES5的时候，会自动判断这个箭头函数的上层是否有this，如果他的上层是global/window, 会直接编译成undefined，因为上面说过严格模式下，禁止this指向全局对象。 如果这个箭头函数上层是一个正常的function， 则会编译成它上层中的this