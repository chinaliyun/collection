`$controller:ctrlreg`
-
用到的controller必须定义，否则会出现这个错误

`$injector:modulerr `
-
找不到被注入的服务，查看一下第三方服务有没有添加到app.module的依赖中，或者页面内有没有引入依赖的JS文件 

`Could not resolve 'main' from state ''`
-
超链接定义到的路由没有定义活着不存在，查看一下超链接的目标和路由表中的字符是否一致，

`$compileLtplrt`; 
-
可能模板文件没有根元素

`$injector:modulerr`
-
模块错误，可能定义了同名的模块

`Cannot use 'in' operator to search`,
-
 独立作用域内调用父级作用域的方法时，1、注意传入的参数要时对象形式，否则会报错 2、父级指令的值里面的函数不要忘记加参数，参数的名称要和传入的key一致，这个参数时为了传递子作用域中过来的参数

