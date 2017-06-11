通过new方法生成的对象实例返回的类型实际上是一个对象，这个对象中包含实例对象的构造函数中的属性和方法
```js
    function add(){
        this.x = 1
    }
    var a = new add();
    a       //add{x:1}
```
每个构造函数都有一个prototype属性，他的值是一个对象，对象中的所有属性和方法都会被构造函数生成的实例对象继承

如果构造函数的prototype属性不为空，对象实例的内容应该是下面这个样子的
```js
    function add(){
        this.x = 1
    }
    add.prototype = {
        y: 2;
    }
    var a = new add();
    a;       
```
输出：
```js
    add{
        x: 1,
        __proto__: {
            constructor: function add(){},
            y: 2,
        }
    }
```
每一个对象实例都有一个constructor属性，这个属性指向他的构造函数
```js
    function add(){}
    var a = new add();
    a.constructor       //function ad(){}
```
但是他调用的实际上是对象实例的__proto__中的constructor方法，也就是其构造函数的prototype对象的constructor方法
```js
    function add(){}
    var p = new add();
    p.constructor 				//	function add(){}
    p.__proto__.constructor     //  function add(){}
    add.prototype.constructor	//	function add(){}
```
对象实例的`__proto__`属性，指向其构造函数的prototype属性，
```js
function add(){}
var p = new add();
p.__proto__ == add.prototype  //	true
```
每一个函数都有一个__proto__属性，指向
