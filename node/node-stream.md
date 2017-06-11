Stream是Node自带的一个流机制，Node里面很多模块都实现了这个接口，由于他是一个抽象的接口，所以我们拿文件的读取和写入来举例

1.读取文件
```js
    //创建一个读取文件流
    var ReadStream = fs.createReadStream();

    //设置读取的编码
    ReadStream.setEncoding('utf-8');

    //为读取文件流添加data事件，这个事件在文件中有数据可读取的时候被触发
    readerStream.on('data', function(chunk) {
       console.log('start read')
    });

    //为读取文件流添加end事件，这个事件在文件中没有数据可读取的时候被触发
    readerStream.on('end',function(){
       console.log('stop read')
    });

    //为读取文件流添加error事件，当读取文件的过程中出现错误的时候被触发
    readerStream.on('error', function(err){
        console.error('error')
    });
```
2.写入文件
```js
    //准备要写入的内容
    var data = 'lisi';

    //创建一个写入文件流
    var writeStream = fs.createWriteStream('output.txt');

    //向文件流中写入指定内容，并指定编码
    writeStream.write(data,'utf-8')

    //结束写入
    writeStream.end()

    //为写入文件流添加finish事件，这个事件在文件写入完成之后被触发
    writeStream.on('finish', function(){
        console.log('write finish')
    })
    //为写入文件流添加error方法，事件在文件写入出现错误的时候被触发
    writeStream.on('error', function(){
        console.error('error')
    })
    console.log('写入完成')
```
上面的例子我们可以看到，读取文件的时候有data,end，error三个事件，写入文件的时候有finish、error两个事件，写入文件的时候，千万不要忘记写入end方法

管道流
-
看了官网的管道流让我想起来gulp的pipe方法，把处理过的结果再用其他方法处理，Node里面的管道流和那个有异曲同工之处，先看例子：
```js
    var fs = require('fs');
    //创建一个读取文件流
    var readStream = fs.createReadStream('demo1.txt');
    //创建一个写入文件流
    var writeStream = fs.createWriteStream('demo2.txt');
    读取demo1.txt的内容，再写入到demo2.txt中
    readStream.pipe(writeStream)
    console.log("执行完毕")
```
我们看到读取之后跟了一个pipe方法，如果这里表示的不够清晰，我们可以这样写就一目了然了
```js
    fs.createReadStream('demo1.txt')
        .pipe(fs.createWriteStream('demo2.txt'))
```
这样写的方式就和gulp的一样了

链式流
-
上面的管道流说的是吧文件处理的结果拿去用另一个方法处理，那么链式流就是把处理的结果进行多次管道操作，看下面的例子
```js
    var fs = require('fs');
    var zlib = require('zlib');
    
    fs.createReadStream('example.txt') //读取文件
        .pipe(zlib.createGzip)       //压缩文件
        .pipe(fs.createWriteStream('example.txt.gz'))    //创建一个压缩包

    console.log("解压完成")
```
那么我们再创建一个解压的文件
```js
    var fs = require("fs");
    var zlib = require('zlib');

    // 解压 input.txt.gz 文件为 input.txt
    fs.createReadStream('input.txt.gz')
      .pipe(zlib.createGunzip())
      .pipe(fs.createWriteStream('input.txt'));
      
    console.log("文件解压完成。");
```
