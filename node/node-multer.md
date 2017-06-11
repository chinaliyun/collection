multer介绍
-
multer是一个NodeJS中间件，用来处理multipart/form-data类型的表单数据，主要用于上传文件的时候，看了一些文档，感觉介绍的都不够清晰

安装和使用
-
```js
    // 安装 
    npm i multer --save
    // 使用
    var multer = require('multer');
```
那么multer中间件的原理是什么？他会给req添加一个files对象，这个对象包含通过表单上传的文件信息,我们先大致看一下，通过中间件获取的文件信息都有哪些：

key | description| 信息存储在哪里
--- | ---|---
fieldname|表单中文件控间的name属性|
originnalname|上传的文件名称|
encoding|文件编码|
mimetype|文件的Mime类型|
size|文件大小，以字节为单位|
destination|保存路径的目录|硬盘
filename|保存的临时文件名|硬盘
path|保存路径的完整路径(目录+文件名)|硬盘
buffer|整个文件的Buffer数据|内存

以下是几种获取文件信息的方式：

1.multer.single()

这个方法需要使用req.file来获取上传的文件，只适用于上传单个文件,single()需要填写正确的文件field_name，否则会报错
```js
    app.post('upload_cache/', multer().single(),function(req,res){
        console.log(req.file) //获取上传的文件信息
        })
```
返回样本：
```js
    {
        //文件信息...
    }
```
2.multer.array()

这个方法适用于上传多个文件，且文件的name属性是一致的；方法返回的是一个数组，数组内包含文件的所有信息，如下所示：
```html
    <form>
        <input type="file" name="file_example1">
        <input type="file" name="file_example2">
    </form>
```
```js
    app.post('upload_cache/', multer().array('file_example', 10),function(req,res){
        console.log(req.files) //获取上传的文件信息,是一个数组
        })
```
其中array的第一个参数是文件数组的名称，第二个参数是显示最大上传的文件数量，超过这个数量的文件不会被处理，但是可以小于这个数量

返回样本：
```js
    [
        {
            //文件信息...
        },
        {
            //文件信息...
        },
    ]
```

3.multer.fields()

这个方法适用于上传多个文件，但区别在于这些文件的name属性名都是不同的
```html
    <form>
        <input type="file" name="file_example1">
        <input type="file" name="file_example1">
        <input type="file" name="file_example2">
    </form>
```
```js
    app.post('upload_cache/', multer().fields(),function(req,res){
        console.log(req.files) //获取上传的文件信息
        })
```
返回样本：
```js
    {
    "file_example1":[
        {
            //文件信息...
        },
        {
            //文件信息...
        },
    ],
    "file_example2":[
        {
            //文件信息...
        }
    ]
}
```
4.multer.none()

这个方法，只接受文本域，任何文件上传到这个模式，都会报错

5.multer.any()

这个方法，会接受一切的内容，文件数组全部都保存在req.files中

安全
-
这里要注意一点：永远不要把multer作为全局中间件使用，尽量在需要处理文件上传的路由上使用，或者添加自定义的一些中间件，来判断上传人员的身份

