Class中定义的方法和属性
-
Class的所有方法(除了constructor)都定义在他的prototype属性上

通过new Class创建的实例的属性，除非是显式定义在其本身(即this)上，否则都是定义在原型(即class)上
```js
    class Foo{
        constructor(x,y){
            this.x = x;
            this.y = y; //这里通过this定义的就是显式定义
        }
        toString(){
            code;   //这个toString方法都是定义在class上，而不是生成的实例上
        }
    }
    var f = new Foo(1,2);
```
一个类必须具有constructor方法，如果没有显式定义，会被默认添加一个空的constructor方法

constructor方法默认返回实例对象(即this),也可以重新指定返回另一个对象

```js
    class Foo{
        constructor(){
            return Obj.create(null)
        }
    }
    new Foo() instanceof Foo    // false
```
class内部定义的所有方法都是 **不可枚举** 的(enumerable)，、ES5中通过prototype定义的方法，默认是可枚举的
```js
    //class内部定义的属性
    class Foo{
        constructor(){}
        toString(){}
        dev()
    }
    Object.keys(Foo.prototype)                    //[],返回指定对象的所有可枚举属性
    Object.getOwnPropertyNames(Foo.prototype)     
    //['constructor','toString']， 返回指定对象自身的所有属性，包含不可枚举属性

    //ES5prototype定义的属性
    function add(){}
    add.prototype.toString = function(){}
    Object.keys(add.prototype)                    //['toString']
    Object.getOwnPropertyNames(add.prototype)     //['constructor','toString']
```
class的name属性
-
Class的name属性 返回跟在class关键字后面的类名，当使用class表达式的时候，name属性返回声明的变量名，而不再是class关键字后面的名称，class后面的名称只可用于 `class内部` 使用，代指当前类
```js
    class Foo{}                 //Foo.name  =  Foo
    let DevName = class Dev{
        getClassName(){
            return Dev.name;
        }
    }
    Dev.name                    // DevName 而不是  Dev
    let d = new DevName();
    d.getClassName              // Dev
    Dev.name                    //Dev is not defined
```
立即执行class
-
与function一样，我们也可以写出立即执行的class实例，这个时候不需要实例化就可以直接使用里面的方法
```js
    let person = new class{
        constructor(name){
            this.name = name
        }
        getName(){
            console.log(this.name)
        }
    }('张三');
    person.getName()         //Myclass
```
变量提升
-
class中不存在变量提升，因此class实例的生成必须写在class声明最后，否则会提示语法错误

严格模式
-
ES6中的类和模块默认使用严格模式，因此不需要使用use strict关键字来指定运行模式

class中的继承
-
class之间通过extends实现继承，子类必须在constructor中加入super方法，否则新建子类的实例会报错，这是因为class的继承机制是先创建父类的实例对象this，在用子类的构造函数修改this，因此如果不调用super方法，就得不到this对象
```js
    class Foo{
        constructor(x,y){
            this.x = x;
            this.y = y;
        }
    }
    class Dev extends Foo{
        constructor(){
            super(x,y)
        }
    }
```
注意： 通过class创建的子类的`__proto__`指向他的父类，子类的prototype的`__proto__`指向他的父类的prototype
```js
    class a{}
    class b extends a{}
    b.__proto__ === a                       //true
    b.prototype.__proto__ === a.prototype   //true
```
理论上，extends可以继承多种类型的值，包含Obejct，Function等

如果一个class属于基类，也就是说不存在继承关系，是一个普通函数的时候，那么他的__proto__属性依然指向Function.prototype
```js
    class A{}
    A.__proto__ === Function.prototype          //true
    A.prototype.__proto__ === Object.prototype  //true
```
有一个特殊的例子是，当一个class继承自null的时候，他的__proto__依然指向Function.prototype
```js
    class A extends null{}
    A.__proto__ === Function.prototype          //true
    A.prototype.__proto__ === undefined         //true
```
与prototype有关的Object方法
-
Object.getPrototypeOf(子类)： 获取指定子类的父类
```js
    class a{}
    class b extends a{}
    Object.getPrototypeOf(b)                       //a
```
super
-
先来理解super是干啥的，super指向父类的实例，子类继承父类的时候，需要在constructor中调用一次super的， 就相当于调用了父类的constructor方法，目的是为了把this继承进来
```js
    class a{
        constructor(a,b){
            this.x = a;
            this.y = b;
        }
    }
    class b extends a{
        constructor(){
            super(2,3)
        }
    }
    new b().x   //2
    new b().y   //3
```
class中支持直接使用super来调用父类中的方法，super关键字代表父类的实例
```js
    class a{
        getName(){
            return 1
        }
    }
    class b extends a{
        say(){
            console.log(super.getName())
        }
    }
    new b().say()   // 1
```
原生构造函数的继承
-
ES5中不允许继承原生构造函数，因为不管是apply还是call都无法获取原生构造函数的内部属性,ES6的出现允许了创建一个继承自原生构造函数的类

Class的取值函数getter和存值函数setter
-
class内部也可以像ES5一样使用取值、存值函数
```js
    class Foo{
        constructor(){
            this.x = 1
        }
        get prop(){
            return this.x
        }
        set prop(v){
            this.x = v
        }
    }
    var f = new Foo();
    f.prop  //1
    f.prop=2;
    f.prop  //2
```
class中的Generator函数
-
class中的函数前面加一个*就表示该方法是一个generator函数
```js
    class Foo{
        *add(){
            yield 1;
            yield 2;
            yield 3;
        }
    }
    var f = new Foo();
    var g = f.add();
    for(let i of g){
        console.log(i)
    }
    //1
    //2
    //3
```
class的静态方法
-
如果class的方法前面添加了static关键字，就说明这个方法是一个静态方法

静态方法不能被实例继承，但可以在子类中用super调用，也可以通过类名直接调用
```js
    class Foo{
        static add(){
            return 1
        }
    }
    class Dev extends Foo{
        static get(){
            return super.add() //直接通过super调用父类中的静态方法
        }
    }
    Dev.get()   // 1， 通过类名直接调用类中的静态方法
    
```
class中的静态属性
-
ES6规定class内部只有静态方法，没有静态属性，ES7中会有相关的提案，

ES7中提供的实例属性方法如下：
```js
    class Foo{
        x = 1;
        constructor(){
            console.log(this.x);
        }
    }
```
静态属性的写法：
```js
    class Foo{
        static x = 1;
        constructor(){
            console.log(this.x);
        }
    }
```
new.target属性
-
这个属性可以确定构造函数是怎么调用的，如果不是通过new命令调用的，会返回undefined
```js
    function foo(name){
        if(new.target !== undefined){
            this.name = name
        }else{
            throw new Error('必须使用new命令生成实例')
        }
    }
```
class内部调用new.target的时候，返回当前类， 子类继承父类的时候，new.target返回子类，可以通过这个属性来创建一些不能独立使用，必须被集继承后才能使用的类
```js
    class Foo{
        constructor(){
            console.log(new.target)
            //class内部调用new.target的时候，返回当前类， 子类继承父类的时候，new.target返回子类
            try{
                if(new.target === Foo) throw new Error('该类不能被直接使用')
            }catch(e){
                console.error(e)
            }
        }
    }
    class Dev extends Foo{
        constructor(){
            super();
        }
    }
    var f = new Foo();  //报错
    var d = new Dev();  //正确
```
Mixin模式的实现
-
暂未弄懂