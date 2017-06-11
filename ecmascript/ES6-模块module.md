export命令
-
export用于规定对外的接口，这个文件内部所有的变量，外部都无法获取，如果希望外部文件能够读取模块内部的变量，必须使用export把这个变量暴露出去

export可以出现在模块的任何位置，但是必须处于模块的顶层，不能写在块里面，后面的import命令也是这样
```js
    var foo;
    function add(){
        export foo;         //这里会报错
    }
    export add;             //这个不会报错
    export function baz(){} //  这个不会报错
```
允许在一个模块中同时存在多个export语句
```js
    export var name = '张三';         
    export var age = 5;         
```
但是为了能在模块底部一眼看清输出了哪些变量，我们可以在底部统一把他们暴露出去
```js
    var name = 'John';
    var age = 3;
    export {name,age};
```
这些暴露出去的名字是可以更改的，需要使用as关键字
```js
    var name = 'John';
    var age = 3;
    export {
        name as Myname,
        age as Myage
    }; 
```
export语句输出的值是动态绑定的，绑定在他所在的模块内
```js
    export var foo = 'foo';
    settimeout(()=>foo='baz', 500)
```
上面模块中的foo会在500毫秒之后变为'baz'。

import命令
-
import命令用来加载其他使用export定义了模块对外接口的JS文件，用法:
```js
    import { name , age , sex} from 'person.js';
```
上面的命令就是加载person.js模块中的name，age，sex三个变量,大括号内的名称必须和person.js中暴露出来的变量名一致，如果想重命名可以使用as关键字
```js
    import { name as Myname , age as Myage, sex as Mysex} from 'person.js';
```
如果export语句后面使用了as关键字，import的时候，必须与export as后面的变量名一致

import命令具有提升效果，他会提升到整个模块的最前面执行，因此下面的例子是不会报错的
```js
    name();
    import {name} from 'person.js'
```
如果模块内需要先加载再输出同一个模块，import和export可以写在一起
```js
    export {name} from 'person.js'
    //等于
    import {name} from 'person.js';
    export {
        name
    }
```
整体加载模块\*与module命令
-
我们可有使用\*用来整体加载一个模块
```js
    import * from 'person.js';
```
这样就可以直接使用模块里面的所有方法，另一个整体加载模块的命令是module
```js
    module person from 'person.js'
    person.name();
    person.age();
```
通过module加载的模块内的所有方法都定义在变量person中

默认输出的变量
-
我们可以用export default来声明输出的默认变量，export default后面可以添加匿名函数或者非匿名函数，import默认变量的时候，不需要{}，并且可以直接重命名默认函数
```js
    //'person.js'
    export default function(){}
    //'student.js'
    import myName from 'person.js'

    //'person.js'
    export default function foo(){}
    //'student.js'
    import myName from 'person.js'
```
因此每个模块内部输出的默认变量，都只能有一个。由于export default的本质就是输出了一个叫做default的变量或方法，所以下面的方法也是可以用的
```js
    //'person.js'
    function add(){}
    export {add as default}
    //'student.js'
    import myAdd form 'person.js'
```
最后，export方法也可以用来输出class

模块的继承
-
模块之间也是可以继承的
```js
    export * from 'person.js';
    export function add(){};
    export foo = 'zhangsan';
```
上面代码中第一句就输出了person.js模块中的所有方法，会忽略掉他里面的default方法，之后又输出了自己模块内部的add方法和foo变量、

模块的加载
-
ES6模块的加载与commonJS最大的区别在于，commonJS是对属性的拷贝，而ES6中是对属性的引用，

CommonJS中的模块输出之后，内部变化不再影响外部的结果，而ES6模块中属性的取值是动态的，

ES6中获取到的模块变量只可以添加属性，不能被重新赋值，就像const一样，如果重新赋值会报错
```js
    //'person.js'
    export person = {name:zhangsan}
    //'app.js'
    import person from 'person.js';
    person = {}     //报错
    person.age = 3  //不会报错
```

ES6模块的使用、
-
对于不支持ES6模块的浏览器，可以通过三种方式来编译成ES5使用
一、babel

二、ES6-module-transpiler

这是Square公司的一个开源转码器，需要安装这个包
    
        npm i es6-module-transpiler -g
然后直接通过compile-modules convert命令将ES6模块文件转码
```js
    compile-modules convert example.js
    //也可以制定转码后的文件名
    compile-modules convert -o example.js new.js
```

三、systemJS

这是一个垫片库，可以在浏览器内加载ES6模块、commonJS模块、AMD模块，将其转为ES5格式
使用的时候先加载system.js文件
```
    <script src="system.js"></script>
    <script>
        //system.js使用System.import方法加载模块文件
        System.import('example.js')
    </script>
```
加载后，返回一个promise对象，就可以继续使用了
```js
    System.import('example.js').then(function(){
        //code...
    })
```