ECMAScript
-
 - 避免使用 eval 或 Function 构造器
 - 改写 eval
 - 如果你需要函数，使用 function
 - 不要使用 with
 - 不要在要求性能的函数中使用 try-catch-finally
 - 隔离 eval 和 with 的使用
 - 尽量不用全局变量
 - 注意对象的隐式换
 - 在要求性能的函数中避免使用 for-in
 - 使用累加形式连接字符串
 - 基本运算比调用函数更快
 - 为 setTimeout() 和 setInterval() 传入函数而不是字符串

DOM
-
 - 重绘和重排
 - 将重排数量降到最低
 - 最小重排
 - 修改文档树
 - 修改不可见的元素
 - 测量
 - 一次改变多项样式
 - 平滑度换速度
 - 避免检索大量节点
 - 通过 XPath 提升速度
 - 避免在遍历 DOM 的时候进行修改
 - 在脚本中用变量缓存 DOM 的值

文档加载
-
 - 避免在多个文档间保持同一个引用
 - 快速历史导航
 - 使用 XMLHttpRequest
 - 动态创建 `<script>` 元素
 - location.replace() 控制历史记录



避免使用 eval 或 Function 构造器
-
每次进行 eval 或调用 Function 构造器，脚本引擎都会启动一些机制来将字符串形式的源代码转换为可执行的代码。这通常会严重影响性能 —— 比如说，直接调用函数的性能是它的 100 倍。

eval 函数尤其糟糕，因为 eval 无法预知传递给它的字符串的内容。代码是在调用 eval 的上下文档中解释，这就意味着编译器无法优化相关上下文，就会留给浏览器很多需要在运行时解释的内容。这就造成了额外的性能影响。

Function 构造器比 eval 要好一点，因为它不影响周围代码的使用，但它仍然相当缓慢。

改写 eval
-
eval 不仅仅是低效，它几乎不用存在。多数使用它的情况都是另存为信息是通过字符串提供的，这些信息被假定用于 eval。下面的示例展示了一些常见的错误：
```js
function getProperty(oString) {
  var oReference;
  eval('oReference = test.prop.' + oString);  return oReference;
}
```
这段代码完成了同样的功能，是它没有使用 eval：
```js
function getProperty(oString) {  return test.prop[oString];
}
```
在 Opera9、Firefox 和 Internet Explorer 中，没有使用 eval 的代码比用了 eval 的代码快 95% 左右，在 Safari 中快 85% 左右。(注意这不包含调函数本身所需要的时间。)

如果你需要函数，使用 function
-
这个例子展示了使用 Function 构造器常见的用法：
```js
function addMethod(oObject, oProperty, oFunctionCode) {
  oObject[oProperty] = new Function(oFunctionCode);
}
addMethod(
  myObject,  'rotateBy90',  'this.angle = (this.angle + 90) % 360');
addMethod(
  myObject,  'rotateBy60',  'this.angle = (this.angle + 60) % 360');
```
下面的代码实现了同样的功能，但没有用 Function 构造林。它通过匿名函数实现，匿名函数可以像其它对象一样被引用：
```js
function addMethod(oObject, oProperty, oFunction) {
  oObject[oProperty] = oFunction;
}
addMethod(
  myObject,  'rotateBy90',
  function() {
    this.angle = (this.angle + 90) % 360;
  }
);
addMethod(
  myObject,  'rotateBy60',
  function() {
    this.angle = (this.angle + 60) % 360;
  }
);
```
不要使用 with
-
虽然 with 能给开发者带来便利，但它可能会影响性能。with 会在引用变量时为脚本引擎构造一个额外的作用域。仅此，会造成少许性能下降。然而，编译期并不能获知这个作用域的内容，所以编译器不会像优化普通的作用域(比如由函数创建的作用域)那样优化它。

有更多的方法给开发者提供使得，比如使用一个普通变量来引用对象，然后通过这个变量来访问其属性。显然只有当属性不是字面类型，比如字符串或布尔型的时候，才能这样做。

看看这段代码：
```js
with(test.information.settings.files) {
  primary = 'names';
  secondary = 'roles';
  tertiary = 'references';
}
```
下面的代码会让脚本引擎更有效率：
```js
var testObject = test.information.settings.files;
testObject.primary = 'names';
testObject.secondary = 'roles';
testObject.tertiary = 'references';
```
不要在要求性能的函数中使用 try-catch-finally
-
try-catch-finally 结构相当独特。与其它结构不同，它运行时会在当前作用域创建一个新变量。在每次 catch 子句运行的时候，这个变量会引用捕捉到的异常对象。这个变量不会存在于脚本的其它部分，哪怕是在相同的作用域中。它在 catch 子句开始的时候创建，并在这个子句结束的时候销毁。

因为这个变量在运行时创建和销毁，并且在语句中代表着一种特殊的情况，某些浏览器不能很有效地处理它。因此如果把它放在一个要求性能的循环中，在捕捉到异常时可能造成性能问题。

异常处理应该尽可能地放在更高层次的脚本中，在这里异常可能不会频繁发生，或者可以先检查操作是否可行以避免异常发生。下面的示例展示了一个循环，在访问的属性不存在时有可能抛出几个异常：
```js
var oProperties = [  'first',  'second',  'third',
  …  'nth'];for(var i = 0; i < oProperties.length; i++) {  try {
    test[oProperties[i]].someproperty = somevalue;
  } catch(e) {
    …
  }
}
```
多数情况下，try-catch-finally 结构可以移动到循环外层。这对语义略有改动，因为如果异常发生，循环就中止了，不管之后的代码是否能继续运行：
```js
var oProperties = [  'first',  'second',  'third',
  …  'nth'];try {  for(var i = 0; i < oProperties.length; i++) {
    test[oProperties[i]].someproperty = somevalue;
  }
} catch(e) {
  …
}
```
某些情况下，try-catch-finally 结构可以通过检查属性或者其它适当的测试来完全规避：
```js
var oProperties = [  'first',  'second',  'third',
  …  'nth'];for(var i = 0; i < oProperties.length; i++) {  if(test[oProperties[i]]) {
    test[oProperties[i]].someproperty = somevalue;
  }
}
```

隔离 eval 和 with 的使用
-
由于这些结构会对性能造成显著影响，应该尽可能的少用它们。但有时候你可能仍然需要使用到它们。如果某个函数被反复调用，或者某个循环在重复执行，那最好不要在它们内部使用这些结构。它们只适合在执行一次或很少几次的代码中使用，还要注意这些代码对性能要求不高。

无论什么情况，尽量将它们与其它代码隔离开来，这样就不会影响到其它代码的性能。比如，把它们放在一个顶层函数中，或者只运行一次并把结果保存下来，以便稍后可以使用其结果而不必再运行这些代码。

try-catch-finally 结构可能会在某些浏览器对性能产生影响，包括 Opera，所以你最好以同样的方式对其进行隔离。

尽量不用全局变量
-
创建临时变量很简单，所以很诱人。然而，因为某些原因，它可能会让脚本运行缓慢。

首先，如果代码在函数或另一个作用域中引用全局变量，脚本引擎会依次通过每个作用域直到全局作用域。局部变量找起来会快得多。

全局作用域中的变量存在于脚本的整个生命周期。而局部变量会在离开局部作用域的时候被销毁，它们占用的内存可以被垃圾收集器回收。

最后，全局作用域由 window 对象共享，也就是说它本质上是两个作用域而不是一个。在全局作用域中，变量总是通过其名称来定位，而不是像局部变量那样经过优化，通过预定义的索引来定位。这最终导致脚本引擎需要花更多时间来找到全局变量。

函数通常也在全局作用域中创建。因此一个函数调另一个函数，另一个函数再接着调其它函数，如此深入下去，脚本引擎就会不断增加往回定位全局变量的时间。

来看个简单的示例，i 和 s 定义在全局作用域中，而函数会使用这些全局变量：
```js
var i, s = ''
function testfunction() {  for(i = 0; i < 20; i++) {
    s += i;
  }
}
testfunction();
```
下面的替代版本执行得更快。在多数当今的浏器中，包括 Opera 9、最新的 Internet Explorer、Firefox、Konqueror 和 Safari，它的执行速度会比之前的版本快 30% 左右。
```js
function testfunction() 
  var i, s = '';  for(i = 0; i < 20; i++) {
    s += i;
  }
}
testfunction();
```
注意对象的隐式转
-
字面量，比如字符中、数、和布尔值，在 ECMAScript 中有两种表现形式。它们每种类型都可以作为值创建，也可以作为对象创建。比如，var oString = 'some content'; 创建了一个字符串值，而 var oString = new String('some content'); 创建了等价的字符串对象。

所有属性和方法都是在字符串对象而不是值上定义的。如果你对字符串值调用属性和方法，ECMAScript 引擎必须用相同的字符串值隐式地创建一个新的字符串对象，然后才能调用方法。这个对象仅用于这一个需求，如果下次再对字符串值调用某个方法，会再次类似地创建字符串对象。

下面的示例的让脚本引擎创建 21 个新的字符串对象。每次访问 length 属性和每次调用 charAt 方法的时候都会创建对象：
```js
var s = '0123456789';for(var i = 0; i <s.length; i++) {
  s.charAt(i);
}
```
下面的示例与上面那个示例等价，但创建了一个对象，它的执行结果更好：
```js
var s = new String('0123456789);for(var i = 0; i < s.length; i++) {
  s.charAt(i);
}
```
如果你的代码经常调用字面量的方法，你就应该考虑把它们转换为对象，就像上面的例子那样。

注意，虽然本文中大部分观点都与所有浏览器相关，但这种优化主要针对 Opera。它也可能影响其它一些浏览器，但在 Internet Explorer 和 Firefox 中可能会慢一些。

在要求性能的函数中避免使用 for-in
-
for-in 循环经常被误用，有时候普通的 for 循环会更合适。for-in 循环需要脚本引擎为所有可枚举的属性创建一个列表，然后检查其中的重复项，之后才开始遍历。

很多时候脚本本身已经知道需要遍历哪睦属性。多数情况下，简单的 for 循环可以逐个遍历那些属性，特别是它们使用有序的数字作为名称的时候，比如数组，或者伪数组(像由 DOM 创建的 NodeList 就是伪数组)。

下面有一个未正确使用 for-in 循环的示例：
```js
var oSum = 0;for(var i n oArray) {
  oSum += oArray[i];
}
```
使用 for 循环会更有率：
```js
var oSum = 0;
var oLength = oArray.length;for(var i = 0; i < oLength; i++) {
  oSum += oArray[i];
}
```
使用累加形式连接字符
-
字符串连接可以非常消耗性能。使用 + 运算符不会直接把结果赋值给变量，它会在内存中创建一个新的字符串用于保存结果，这个新的字符串可以赋值给变量。下面的代码展示了一个常见的字符串连接：
```
a += 'x' + 'y';
```
这段代码首先会在内存中创建一临时的字符串保存连接的结果 xy，然后将它连接到 a 的当前值，再将最终的连接结果赋值给 a。下面的代码使用了两条命令，但因为它每次都是直接赋值，所以不会使用临时字符串。当今许多浏览器中运行这段代码速度会快 20%，而且只需要更少的内存，因为它不需要暂存连接结果的临时字符串：
```js
a += 'x'
a += 'y';
```
基本运算比调用函数更
-
虽然普通代码中不需要太注意，但在要求性能的循环和函数中还有办法提高性能——把函数调用替换为等价的基本调用。比如对数组调用 push 方法就会比直接通过数组的尾部索引添加元素更慢。再比如对于 Math 对象来说，多数时候，简单的数学计算会比调用方法更恰当。
```js
var min = Math.min(a,b)
A.push(v);
```
下面的代码做了同样的事情但性能更好：
```js
var min = a < b ? a b;
A[A.length] = v;
```
为 setTimeout() 和 setInterval() 传入函数而不是字符串
-
setTimeout() 和 setInterval() 方法与 eval 类似。如果传递给它们的是字符串，在等待指定的时间之后，会跟 eval 一样进行计算，也会对性能造成同样的影响。

不过这些方法都会接收函数作为第一个参数，所以可以不用传入字符串。作为参数传入的函数会在一定延迟之后调用，但它们可以在编译期进行解释和优化，最终会带来性能提升。这里有个使用字符串作为参数的典型示例：
```js
setInterval('updateResults()', 1000)
setTimeout('x+=3; prepareResult(); if(!hasCancelled){ runmore() }', 500);
```
第一种情况可以直接引用函数。第二种情况可以使用匿函数来封装代码：
```js
setInterval(updateResuts, 1000);
setTimeout(function () {
  x += 3;
  prepareResult();  if(!hasCancelled) {
    runmore();
  }
}, 500);
```
注意，所有情下超时和间隔时间都可能不准确。一般来说，浏览器延迟的时间可能会略长一些。有些人会把请求时间稍微提前一点来进行补偿，其他人会尝试每次都等待正确的时间。像 CPU 速度、线程状态和 JavaScript 加载等因素都会影响延迟的准确性。多数浏览器不会按 0 毫秒进行延迟，它们会施以最小的延迟来代替，这个延迟一般在 10 到100 毫秒之间。


02、DOM
-
总的来说，有三个主要因素会导致 DOM 性能不佳。一是使用脚本进行了大量的 DOM 操作，比如通过收到的数据创建一棵树。二是脚本触发了太多重排或者重绘。三是脚本使用了低性能的方法来定位 DOM 树中的节点。

第二点和第三点非常普遍，也非常重要，所以首先解决它们。

重绘和重排
-
有东西从不可见变为可见，或者反之，但没有改变文档布局，就会触发重绘。比如为某个元素添加轮廓线，改变背景色或者改变 visibility 样式等。重绘很耗性能，因为它需要引擎搜索所有元素来决定什么是可见的，什么应该显示出来。

重排带来更大的变化。如果对 DOM 树进行了操作，或者某个样式改变了布局，比如元素的 className 属性改变时，或者浏览器窗口大小改变的时候。引擎必须对相关元素进行重排，以确定现在各个部分应该显示在哪里。子元素也会因父元素的变化重排。显示某个被重排的元素之后的元素也需要重新计算新的布局，与最开始的布局不同。由于子孙元素大小的改变，祖先元素也需要重排以适应新的大小。最后还需要对所有元素进行重绘。

重排特别消耗性能，它是造成 DOM 脚本缓慢的主要原因之一，这对处理器性能不高的设备，比如电话，尤其显著。多数情况下它相当于重新布局整个页面。

将重排数量降到最低
-
很多时候脚本都需要做一些引起重绘或者重排的事情。动画就是基于重排的，而大家仍然希望看到它。因此在 Web 开发中，重排不可避免，要保证脚本跑得飞快，就必须在保证相同整体效果的前提下将重排保持在最低限度。

浏览器可以选择在脚本线程完成后进行重排，显示变化。Opera 会等到发生了足够多的变化，经过了一定的时间，或者脚本线程结束。也就是说，如果在同一个线程中发生的变化足够快，它们就只会触发一次重排。然而，考虑到 Opera 运行在不同速度的设备上，这种现象并不保证一定会发生。

注意某些元素在重排时显示慢于其它元素。比如重排一个 table 需要 3 倍于等效块元素显示的时间。

最小重排
-
一般的重排会影响到整文档。文档中需要重排的东西越多，重排花的时间就越长。绝对(absolute)定位和固定(fixed)定位的元素不会影响主文档的布局，所以对它们的重排不会引起其它部分的连锁反应。文档中在它们之后的内容可能需要重绘来呈现变化，但这也远比一个完整的重排好得多。

因此，动画不需要应用于整个文档，它最好只应用在一个固定位置的元素上。大多数动画都只需要考虑这个问题。

修改文档树
-
修改文件树 会 导致重排。在 DOM 中添加新的幸免于难、改变文本节点的值、或者修改各种属性，都足以引起重排。多次连续地改变可能导致多次重排。因此，总的来说，最好在一段未显示出来的 DOM 树片段上进行多次改变，然后用一个单一的操作把改变应用在文档的 DOM 中。
```js
var docFragm = document.crateDocumentFragment();
var elem, contents;for(var i = 0; i < textlist.length; i++) {
  elem = document.createElement('p');
  contents = document.createTextNode(textlist[i]);
  elem.appendChild(contents);
  docFragm.appendChild(elem);
}
document.body.appendChild(docFragm);
```
修改文档树也可以通过克隆一个素实现，在修改完成之后将之替换掉文档树中的某个元素，这样只会导致一次重排。注意，如果元素中包含任何形式的控制，就不要使用这个方法，因为如果用户修改了它们的值不会反映在主要的 DOM 树上。如果你需要依赖附加在这个元素或其子元素上的事件处理函数，那么也不要使用这个方法，因为这些附着关系不会被克隆。
```js
var original = document.getEementById('container');
var cloned = original.cloneNode(true);
cloned.setAttribute('width', '50%');
var elem, contents;for(var i = 0; i < textlist.length; i++) {
  elem = document.createElement('p');
  contents = document.createTextNode(textlist[i]);
  elem.appendChild(contents);
  cloned.appendChild(elem);
}
original.parentNode.replaceChild(cloned, original);
```
修改不可见的元素
-
如果某个元素的 display 样式设置为 none，就不会对其进行重绘，哪怕它的内容发生改变。这都是因为它不可见，这是一种优势。如果需要对某个元素或者它的子元素进行改变，而且这些改变又不能合并在一个单独的重绘中，那就可以先设置这个元素的样式为 display:none，然后改变它，再把它设置为普通的显示状态。

不过这会造成两次额外的重排，一次是在隐藏元素的时候，另一次是它再次显示出来的时候，不过整体效果会快很多。这样做也可能意外导致滚动条跳跃。不过把这种方式应用于固定位置的元素就不会导致难看的效果。
```js
var posElem = document.getElmentById('animation');
posElem.style.display = 'none';
posElem.appendChild(newNodes);
posElem.style.width = '10em';
// Other changes…
posElem.style.display = 'block';
```
测量
-
如前所述，浏览器会缓存一些变化，然后在这些变化都完成之后只进行一次重排。不过，测量元素会导致其强制重排。这种变化可能会，也可能不会引起明显地重绘，但重排仍然会在幕后发生。

这种影响发生在使用像 offsetWidth 这样的属性，或者 getComputedStyle 这样的方法进行测量的时候。即使不使用这些数字，只要使用了它们，浏览器仍然会缓存改变，这足以触发隐藏的重排。如果这些测量需要重复进行，你就得考虑只测量一次，然后将结果保存起来以备后用。
```js
var posElem = document.getElementById('aimation');
var calcWidth = posElem.offsetWidth;
posElem.style.fontSize = (calcWidth / 10) + 'px';
posElem.firstChild.style.marginLeft = (calcWidth / 20) + 'px';
posElem.style.left = ((-1 * calcWidth) / 2) + 'px';
// Other changes…
```
一次改变多项样式
-
就像改变 DOM 树一样，也可以同时进行几项相关样式的改变，以尽可能减少重绘或重排次数。常见的方法是一次设置一个样式：
```js
var toChange = document.gtElementById('mainelement');
toChange.style.background = '#333';
toChange.style.color = '#fff';
toChange.style.border = '1px solid #00f';
```
那种方式会造成多重排或重绘。主要有两种方法可以做得更好。如果元素本身需要应用的几个样式，而它们的值都是已知的，那就可以修改元素的 class，并在这个 class 中定义所有新样式：
```css
div 
  background: #ddd;
  color: #000;
  border: 1px solid #000;}
div.highlight {
  background: #333;
  color: #fff;
  border: 1px solid #00f;}
…
document.getElementById('mainelement').className = 'highlight';
```
第二种方法是对为元素义一个新的样式属性，而不是一个个地指定样式。多数情况下这适用于像动画这样的动态变化，新的样式预先并不知道。这通过 style 对象的 cssText 属性实现，或者通过 setAttribute 实现。Internet Explorer 不支持第二种方式，所以只能使用第一种。一些旧的浏览器，包括 Opera 8，要使用第二种方式，不能使用第一种。因此，简单的办法是检查是否支持第一种方式，如果支持，使用它，否则使用第二种。
```js
var posElem = document.getElementById('animation');
var newStyle = 'background: ' + newBack + ';' +  'color: ' + newColor + ';' +  'border: ' + newBorder + ';';
if(typeof(posElem.style.cssText) != 'undefined') {
  posElem.style.cssText = newStyle;
} else {
  posElem.setAttribute('style', newStyle);
}
```
平滑度换速度
-
开发者总是希望通过使用更小的间隔时间和更小的变化，让动画尽可能平滑。比如，使用 10ms 的时间间隔，每次移动 1 个像素实现移动动画。快速运行的动画在某些 PC 或某些浏览器中会运行良好。但是，10ms 几乎已经是浏览器能在不 100% 占用大多数台式机 CPU 的情况能实现的最小时间间隔。有一些浏览器实现不了 —— 对于多数浏览器来说，每秒进行 100 次重排实在太多了。低功耗计算机或低功耗设备上的浏览器无法以这样的速度执行，动画会给人以缓慢和卡顿的感觉。

有必要使用违背一下开发者的意愿，使用动画的平滑度来换取速度。将时间间隔改变为 50ms，动画每次移动 5 个像素，这样需要的处理能力更少，也会让动画在低功耗处理器上运行起来快得多。

避免检索大量节点
-
在试图找到某个特定节点，或者某个节点的子集时，应该使用内置的方法和 DOM 集合来缩小搜索范围，使之在尽可能少的节点内进行搜索。比如，如果你想在文档中找到一个具有某个特定属性的未知的元素，可能这样做：
```js
var allElements= document.getElementsByTagName('*');
for(var i = 0; i < allElements.length; i++) {  
  if(allElements[i].hasAttribute('someattr')) {
    // …
  }
}
```
即使我们忽略像 Xath 这样的高级技术，那个例子中仍然存在两个使之变慢的问题。首先，它搜索了每一个元素，根本没有尝试缩小范围。第二，它在找到了需要的元素之后并没有中止搜索。假如已经知道那个未知的元素在一个 id 为 inhere 的 div 中，下面的代码会好很多：
```js
var allElements = documet.getElementById('inhere').getElementsByTagName('*');
for(var i = 0; i < allElements.length; i++) {  
  if(allElements[i].hasAttribute('someattr')) {
    // …    break;
  }
}
```
如果那个未知的元素那个 div 的直接子级，这种方法可能会更快，这取决于 div 的子孙元素的数量，将之与 childNodes 集合的 length 比较：
```js
var allChildren = documet.getElementById('inhere').childNodes;
for(var i = 0; i < allChildren.length; i++) {  
  if(allChildren[i].nodeType == 1 && allChildren[i].hasAttribute('someattr')) {
    // …    break;
  }
}
```
基本的思路是可能避免手工步入 DOM。有许多在各种情况下表现更好的东西来代替 DOM，比如 DOM 2 Traversal TreeWalker，可用于代替递归遍历 childNodes 集合。

通过 XPath 提升速度
-
一个简单的示例是在 HTML 文档中使用 H2 - H4 创建一个目录，这些元素可以出现在不同的地方，没有任何适当的结构，所以不能用递归来获得正确的顺序。传统的 DOM 会采用这样的方法：
```js
var allElements = document.getElementByTagName('*');
for(var i = 0; i < allElements.length; i++) {  
  if(allElements[i].tagName.match(/^h[2-4]$/i)) {
    // …
  }
}
```
在可能包含了 2000 个元的文档，这会导致显著的延迟，因为需要分别检查它们每一个。XPath，当它得到原生支持的时候，能提供更快的方法。对 XPath 查询引擎的优化可以比直接解释 JavaScript 快得多。在某些情况下，甚至高达两个数量级的速度提升。下面的示例与上面的传统示例等效，但使用 XPath 提升了速度。
```js
var headings = documet.evaluate('//h2|//h3|//h4', document, null, XPathResult.ORDERED_NODE_ITERATOR_TYPE, null);
var oneheading;
while(oneheading = headings.iterateNext()) {
  // …
}
```
下面的代码综合上面的两个版本，在 XPath 可用的时候使用 XPath，否则回到传统 DOM 方法：
```js
if( document.evuate ) {
  var headings = document.evaluate('//h2|//h3|//h4', document, null, XPathResult.ORDERED_NODE_ITERATOR_TYPE, null);
  var oneheading;  
  while(oneheading = headings.iterateNext()) {
    // …
  }
} else {
  var allElements = document.getElementsByTagName('*');  
  for(var i = 0; i < allElements.length; i++) {    
    if(allElements[i].tagName.match(/^h[2-4]$/i)) {
      // …
    }
  }
}
```
避免在遍历 DOM 的时候进行修改
-
对于某些类型的 DOM 集合，如果你的脚本在这些集合中检索的时候改变了相关元素，集合会立即发生变化而不会等你的脚本运行结束。这包含 childNodes 集合，以及 getElementsByTagName 返回的节点列表。

如果你的脚本在像这样的集合中检索，同时又在向里面添加元素，那你可能进入一个无限循环，因为在到达终点前不断的往集合内添加项。不过，这不是唯一的问题。这些集合可能被优化以提升性能。它们能记住长度和脚本引用的最后一个索引，以便在增加索引的时候，能迅速引用下一个节点。

如果你修改了 DOM 树的任意部分，哪怕它不在集合中，集合也必须重新寻找新的条目。这样做的话，它就不能记住最后的索引或长度，因为这些可能已经变化，之前所做的优化也就失效了：
```js
var allPara = document.getElementsByTagName('p');
for(vr i = 0; i < allPara.length; i++) {
  allPara[i].appendChild(document.createTextNode(i));
}
```
在 Opera 中，下面等效的代码性能要好十倍，一些当的浏览器，比如 Internet Explorer 也是如此。它的工作原理是先建立一个静态元素列表用于修改，然后遍历这个静态列表来进行修改。以此避免对 getElementsByTagName 返回的列表进行修改。
```js
var allPara = document.getElementsByagName('p');
var collectTemp = [];
for(var i = 0; i < allPara.length; i++) {
  collectTemp[collectTemp.length] = allPara[i];
}
for(i = 0; i < collectTemp.length; i++) {
  collectTemp[i].appendChild(document.createTextNode(i));
}
collectTemp = null;
```
在脚本中用变量缓存 DOM 的值
-
DOM 返回的某些值是不缓存的，它们会在再次调用的时候重新计算。getElementById 方法就是其中之一，下面的代码就比较浪费性能：
```js
document.getElementById('test').property1 = 'value1'
document.getElementById('test').property2 = 'value2';
document.getElementById('test').property3 = 'value3';
document.getElementById('test').property4 = 'value4';
```
这段代码对同一个对象查找了四次。下面的代码只会查找一次并保下来。对于单独一个请求来说，这样的速度可能没有变化，或者会因此赋值变得稍慢一点。但在后续操作中使用缓存值之后，对当今的浏览器来说，命令运行的速度会快五到十倍。下面的命令与上面示例中的等效：
```js
var sample = document.getElementById('test')
sample.property1 = 'value1';
sample.property2 = 'value2';
sample.property3 = 'value3';
sample.property4 = 'value4';
```
03文档加载
-
避免在多个文档间保持同一个引用
-
如果一个文档访问了另一个文档的节点或者对象，应该避免在脚本使用完它们之后仍然保留它们的引用。如果某个引用保存在当前文档的全局变量中，或者保存在某个长期存在的对象的属性中，通过将其设置为 null，或者通过 delete 来清除它。

原因在于，如果另一个文档已经销毁，比如原来显示在弹出窗中而现在这个窗口关闭了，当前文档中保存的引用通常仍然会使其 DOM 树或者脚本环境在 RAM 中存在，哪怕文档本身已经不在加载状态了。在框架页面，内联框架页面或 OBJECT 元素中同样存在这个问题。
```js
var remoteDoc = parent.frames['sieframe'].document;
var remoteContainer = remoteDoc.getElementById('content');
var newPara = remoteDoc.createElement('p');
newPara.appendChild(remoteDoc.createTextNode('new content'));
remoteContainer.appendChild(newPara);
// Remove references
remoteDoc = null;
remoteContainer = null;
newPara = null;
```
快速历史导航
-
Opera (和很多其它浏览器) 默认使用快速历史导航。当用户在浏览器历史上前进或回退的时候，页面的状态及其中的脚本都被保存了。当用户回到某个页面的时候，它会像从未离开过一样继续运行，文档不会再次加载和初始化。这样做的结果是对用户进行快速响应，也可以使加载缓慢的 Web 应用唾弃在导航过程中表现得更好。

尽管 Opera 提供了一种方法被 创造出来控制这种行为，最好在任何可能的地方都让它使用快速历史导航模式。也就是说，脚本会应该尽量避免做会导致这种行为失败的事情。这就包括了在表单提交时禁用表单控件、菜单项被点击之后就不再有效、离开页面时的淡出效果使内容模糊不清或不可见。

使用 onunload 监听器是比较简单的解决办法，可以通过它重置淡出效果，或者使表单控件变为可用。不过得注意，某些浏览器，比如 Firefox 和 Safari，为 unload 事件添加监听器会导致快速历史导航失效。此外，禁用提交按钮在 Opera 中也会导致快速历史导航失效。
```js
window.onunload = function () 
  document.body.style.opacity = '1';
};
```
使用 XMLHttpRequest
-
这并非对所有项目都适用，但这个方法能有效减少从服务器接收的内容，同时可以避免页面加载带来的脚本环境的破坏和再造。最初，页面以正常的方式加载，之后再通过 XMLHttpRequest 来加载最小需求的新内容。这会让 JavaScript 环境保持下来。

不过需要注意，这种访求可能会导致问题。总的来说，它完全打破了历史导航。虽然这种方法可以通过将信息保存在内联框架中来伪造历史，但这违背了使用 XMLHttpReqest 的首要目的。因此，请谨慎地，只在它所造成的变化不需要回退的时候使用它。这种方法也有可能对辅助设备造成混乱，因为辅助设备感受不到页面上的 DOM 的变化。所以最好在确保不会出现问题的情况下使用这个方法。

如果不允许 JavaScript，或者浏览器不支持 XMLHttpReqeust，这种方法就不可用。解决这个问题最简单的方法就是使用一个正常的链接，指向新页面。然后为这个链接添加事件处理函数，在链接被点击的时候进行检查。事件处理函数可以检测出是否支持 XMLHttpReqest，如果支持，则加载新数据并阻止链接的默认行为。一量数据加载完成，就可以用来替换页面的某些内容，然后销毁请求对象，以允许垃圾回收释放内存。
```js
document.getElementById('nextlink').onclick = funtion() {  if(!window.XMLHttpRequest) {    return true;
  }
  var request = new XMLHttpRequest();
  request.onreadystatechange = function() {    if( request.readyState != 4 ) {      return;
    }
    var useResponse = request.responseText.replace(/^[\w\W]*<div id="container">|<\/div>\s*<\/body>[\w\W]*$/g , '');
    document.getElementById('container').innerHTML = useResponse;
    request.onreadystatechange = null;
    request = null;
  };
  request.open('GET', this.href, true);
  request.send(null);  return false;
}
```
动态创建 `<script>` 元素

加载和处理脚本需要时间，但有些时候，加载了脚本却从未使用。加载这样的脚本纯粹是在浪费时间和资源，最好根本就不要加载不使用的脚本。通过一个简单的加载器脚本可以检查其它脚本是否会用到，只有在脚本实际用到的时候才创建脚本元素。

理论上来说，在页面加载完成之后可以通过 SCRIPT 元素来加载额外的脚本并通过 DOM 添加到文档中。当前所有主流浏览器都支持这样做，但是它实际上可能是在浏览器上请求而不是立即加载脚本。另外，如果需要在页面加载完成之前加载脚本，就最好在脚本加载的过程中进行检查并使用 document.write 来创建脚本标签。千万记得要转义斜杠以免过早结果当前脚本：
```js
if(document.createElement && document.childNodes){
  document.write('<\/script>');
}if(window.XMLHttpRequest) {
  document.write('<\/script>');
}
```
location.replace() 控制历史记录

偶尔是需要使用脚本来改变页面地址。最典型的做法是给 location.href 赋予一个新地地址。这样做会添加一个历史记录，同时加载一个新的页面，这和激活一个普通的链接一样。

在某些情况下，并不希望出现一条额外的历史记录，因为用户不需要回到之前的页面。如果在内存特别重要的环境下，这样做就非常有用。当前页面使用的内存可以通过替换历史记录来得到重新利用，使用 location.replace() 方法就可以做到。
`location.replace('newpage.html');`
请注意，该页可能仍然保留在缓存中，并可能在那里使用内存，但不会用到像保存在历史记录里那么多。