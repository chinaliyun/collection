>由于JavaScript自身没有二进制数据类型，所以Node定义了一个Buffer类，来存放二进制数据的缓存区，它随着Node内核一起发布

一句话就是Buffer是一种存放二进制数据的工具

JS中的二进制
-
在Javascript中如果想把一个数字转成二进制的方法是：
```js
    var n = 5;
    n.toString(2)   //'101'
```
把一个字符串转为二进制的方法
```js
    var str = 'baidu';
    var chr = ''
    for(i in str){
        var uni = str[i].charCodeAt().toString(2); //返回字符的unicode编码，类型是十进制的数字。后面的toString(1)目的是把unicode编码转为二进制
        if(chr == ''){
            chr+=uni
        }else{
            chr = chr+' '+uni
        }
    }
```
    console.log(chr) //这里获取的就是字符串的完整的二进制代码了
那么如果想从二进制转成字符串，同样的道理，要先转成十进制的数字，再使用String.fromCharCode()方法转成字符串

Buffer的创建
-
方法有三种

1.指定buffer对象的字节长度

```js
    var buf = new Buffer(10);
```
2.通过给定的数组创建buffer实例
    
```js
    var buf = new Buffer([119,119]);
```
3.通过字符串创建buffer实例
    
```js
    var buf = new Buffer('buffer','utf-8')
```
utf-8只是默认的编码方式，buffer同样支持其他编码，比如：ascii ucs2  base64 hex等

new Buffer().write(string[, offset[, length]][, encoding])
-
write方法为buffer对象写入字符串，返回的是Buffer对象的长度
参数：

    string      即将写入缓冲区的字符串
    offset      开始写入的字符串的起始位置，默认是0
    length      指定写入的字节长度，默认是Buffer.length
    encoding    写入的字符的编码，默认是utf-8
如果Buffer对象提前指定了字节长度，如果写入的内容超过了字节长度，就只会写入部分字符串,比如

```js
    var buf = new Buffer(1);
    var len = buf.write('zhangsan');
```
    console.log(len)    // 1


new Buffer().toString()
-
这个方法会读取Buffer对象，参数：

    encoding    指定读取使用的编码
    start       指定开始读取的位置，默认是0
    end         指定结束读取的位置，默认是buffer的末尾


Buffer.concat(bufferArray,length)
-
这个方法用于合并buffer对象,第一个参数是要合并的buffer数组，第二个参数指定合并后的总长度

```js
    var buffer1 = new Buffer('菜鸟教程 ');
    var buffer2 = new Buffer('www.runoob.com');
    var buffer3 = Buffer.concat([buffer1,buffer2]);
    console.log("buffer3 内容: " + buffer3.toString());
```

new Buffer().compare(anotherBuffer)
-
这个方法用于比较另外一个Buffer对象,会返回一个数字，表示"前面的buffer对象在后一个buffer对象之前，之后，或相同"， 这句话是官方的翻译，在我看来可以这么理解，我们先看几个例子：

```js
    var buffer1 = new Buffer('ABC');
    var buffer2 = new Buffer('ABCD');
    buffer1.compare(buffer2) // -1

    var buffer1 = new Buffer('BC');
    var buffer2 = new Buffer('ABCD');
    buffer1.compare(buffer2) // 1

    var buffer1 = new Buffer('ABCD');
    var buffer2 = new Buffer('ABCD');
    buffer1.compare(buffer2) // 0
```
三个例子看完之后，我们对`Buffer.conpare`就可以用另一种方法来解释：它的计算其实类似于`indexOf`，`buf1.compare(buf2)`,可以把它看做是`buf2.indexOf(buf1)`,这样就好理解他返回的按个数字了

```js
    buf2 === buf1               // 0
    buf2.indexOf(buf1) < 0      // -1
    buf2.indexOf(buf1) > 0      // 1
```
    