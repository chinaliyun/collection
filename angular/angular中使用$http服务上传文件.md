之前上传文件都比较直接的使用form或者jQuery上传表单，在一个项目中不想引入jQuery ，打算直接用angular的$http服务上传文件试试，代码如下
```
$http({
    url: 'http://192.168.0.136:8077'+url,
    // url: 'http://localhost:8888/index.php',
    method: "POST",
    headers: {
        "Content-Type":undefined,
    },
    transformRequest: function(){
        formData.append('token','dn892hr29');
        return formData;
    }
})
```
这段代码中，`Content-Type: undefined`指定使用angular的$http服务的默认类型

transformRequest来指定转换send出去的数据格式，具体是怎么转换的有待查询，也没找到相关的中文文档