- `@`把属性的值解析为字符串，
- `=`把属性的值解析为表达式，方便引用父级作用域的模型，用作双向绑定使用
- `&`把属性作为隔离作用域的模型，值解析为表达式，方便引用父级作用域的模型，如过被引用的模型的值是一个函数，属性值要写成`模型名称()`的模式，如果是非函数，写成`模型名称`即可
