类的修饰器
-
ES6中是不支持类的属性和静态属性的，这属于ES7的提案

而修饰器是一个表达式，用于修改类的行为，在我看来他可以用来给类或者实例添加属性。例如：
```js
    function testable(target){
        target.isTestable = true;
    }
    @testable
    class Foo{}
    Foo.isTestable      //true
```
上面的testable函数就是一个修饰器函数，`@testable`就是一个修饰器，他为类Foo添加了一个静态属性isTestable

修饰器函数可以接受三个参数：目标函数、属性名、该属性的描述对象(注意这里不是描述符，描述对象值得是对象的某个属性的描述对象的descriptor)，后两个是可以省略的

修饰器是可以带参数的，如果把testable函数重新包装一下，就可以实现根据目标的不同生成不同的静态属性，
```js
    function testable(value){
        return function (target){
            target.isTestable = value
        }
    }
    @testable(true)
    class Foo{}
    Foo.isTestable      //true

    @testable(true)
    class Dev{}
    Dev.isTestable      //false
```
但是要记住，上面的属性都是添加在类上的，

实例的修饰
-
那么如果要为类的实例添加方法呢？ 我们可以在修饰器函数中为目标类的prototype属性添加方法
```js
    function testable(target){
        target.prototype.isTestable = true;
    }
    @testable
    class Foo{}
    var f = new Foo();
    f.isTestable        //true
```
方法的修饰器
-
修饰器还可以用来修饰类里面的方法和属性，这个时候就要用到修饰函数的第二和第三个参数了，修饰函数必须要把第三个参数返回，例如：
```js
    function readonly(target,name,descriptor){
        descriptor.writable = false;
        return descriptor
    }
    class Foo{
        @readonly
        get(){}
    }
```
修饰器后面的参数是根据关键字后面跟着的方法获取的，上面的例子中target指向Foo.prototype，name指向Foo类中get方法， descriptor指向get方法的描述对象，他的结果和
```js
    Object.defineProperty(Foo.prototype,'get',{
        writable: false
        })
```
的结果是一致的

使用babel编译decorator
-
根据[Bebal官网](http://babeljs.io/docs/plugins/transform-decorators/#simple-class-decorator)所说，v6版本已经不支持decorator的编译了，如果想编译修饰器，可以使用v5版本，或者使用第三方插件babel-plugin-transform-decorators-lefacy,具体请查看官网
























