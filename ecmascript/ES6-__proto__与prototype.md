对象的`__proto__`
-
任何对象都有一个叫`__proto__`的东西（注意这里是2个下划线，不是1个），可以用Object.getPrototypeOf(foo)来获得

`__proto__`和prototype是两个东西：

`__proto__`是每一个对象都有的，不仅局限于函数。我们说的“原型链”指的就是这个`__proto__`



prototype
-
prototype是函数特有的，将这个函数用new运算符生成实例后，实例的`__proto__`就是这个函数的prototype
```js
    function add(){}
    var a = new add();
    a.__proto__ === add.prototype   //true
```
关系：
-
数据类型(Function, String, Array等)本身也是一个函数，因为我们都是通过new方法生成新的对象，因此数据类型本身的`__proto__`是Function.prototype
```js
    Function.__proto__ === Function.prototype   //true
    Error.__proto__ === Function.prototype      //true
    String.__proto__ === Function.prototype      //true
    等等
```

再往下说的话，然后`Function.prototype.__proto__`自然是一个Object对象，所有Object对象类型的__proto__都指向Obejct.prototype,所以Function.prototype的`__proto__`是Object.prototype
```js
    Function.prototype.__proto__ === Object.prototype //true
```

最后Object.prototype的`__proto__`是null，注意是null不是undefined，也就是说这是人为设定的且就是设定了null，而不是“没有`__proto__`”
```js
    Object.prototype.__proto__  === null    //true
```

所以JavaScript里有一句话，叫“万物皆继承自null”，不少人听了这话就一口血……

