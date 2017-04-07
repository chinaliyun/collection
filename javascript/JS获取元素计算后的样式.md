一、碎碎念~前言
-
我们都用过jQuery的CSS()方法，其底层运作就应用了getComputedStyle以及getPropertyValue方法。

对于那些只想混口饭吃的人来讲，晓得CSS()如何使用就足够了。对于希望在JS道路上越走越远的来人说，简单了解一些JS库底层实现对自己的学习很有帮助。可能谈不上信手拈来的使用，至少对创造一些创意新颖的新技术拓宽了思路。

jQuery为何受欢迎，其中原因之一就是方法名称比较短。好比打架一样，块头大的潜意识认为厉害，就不由得心生畏惧，退避三舍；小个子（或村妇，小孩）嘛，自然以为是软豆腐，愿意接受作为对手。
功夫剧照 周星驰被村妇K 张鑫旭-鑫空间-鑫生活 功夫剧照 周星驰找小孩单挑 结果…… 张鑫旭-鑫空间-鑫生活

因此，当看到类似getComputedStyle这么长的方法名的时候，我们身体里那个懒惰小人突然就醒了：哎哟哟，这东西，就跟放心食品一样，几年才见一回。看上去又蛮复杂，想到晚上还要跟妹子聊天。这东西，就让它从旁边过吧，反正不会掉块肉的。

网上不是有这么个段子嘛：可怕的是，比你聪明的人比你更勤奋。那么，这里的“勤奋”的差异体现在什么地方呢？就拿这个getComputedStyle举例：懒惰青年是看一下，立马像看见凤姐一样视线离开；普通青年是看一下，发现自己不了解，百一下或谷一下，熟悉了解之；勤奋青年是不仅了解，还抽出时间实践之（如做个简单demo测试），熟悉方法的特异性，验证一些观点正确性。

您可以按照你现在的心理状态看看你是哪类青年：如果此时，您已经对本文的内容没有兴趣了（当然，您的工作与JS关系亲密），您是……（你懂的）；如果您看完本文内容，发现，还是有不少收获，心中回味下，然后ctrl+w去其他地方觅食，不再回来，那您是普通青年；如果您看完本文，然后对所说的一些内容作了额外的测试，例如IE9浏览器是否支持测试等，并通过评论形式指出可能的错误，不得不说您是勤奋青年，假以时日，必有一番技术作为。从我文章的些评论来看，有不少同行就是这样的勤奋人儿。

の, 好久没写文章，话又多了，打住，进入正题。

二、getComputedStyle是？
-
getComputedStyle是一个可以获取当前元素所有最终使用的CSS属性值。返回的是一个CSS样式声明对象([object CSSStyleDeclaration])，只读。

语法如下：
```
var style = window.getComputedStyle("元素", "伪类");
```
例如：
```
var dom = document.getElementById("test"),
    style = window.getComputedStyle(dom , ":after");
```
就两个参数，大家都懂中文的，没什么好说的。只是额外提示下：Gecko 2.0 (Firefox 4 / Thunderbird 3.3 / SeaMonkey 2.1) 之前，第二个参数“伪类”是必需的（如果不是伪类，设置为null），不过现在嘛，不是必需参数了。

三、getComputedStyle与style的区别
-
我们使用element.style也可以获取元素的CSS样式声明对象，但是其与getComputedStyle方法还有有一些差异的。

- 只读与可写

正如上面提到的getComputedStyle方法是只读的，只能获取样式，不能设置；而element.style能读能写，能屈能伸。
- 获取的对象范围

getComputedStyle方法获取的是最终应用在元素上的所有CSS属性对象（即使没有CSS代码，也会把默认的祖宗八代都显示出来）；而element.style只能获取元素style属性中的CSS样式。因此对于一个光秃秃的元素`<p>`，getComputedStyle方法返回对象中length属性值（如果有）就是190+(据我测试FF:192, IE9:195, Chrome:253, 不同环境结果可能有差异), 而element.style就是0。

四、getComputedStyle与defaultView
-
如果我们查看jQuery源代码，会发现，其css()方法实现不是使用的window.getComputedStyle而是document.defaultView.getComputedStyle，唷？这是怎么一回事？
jQuery源码使用document.defaultView.getComputedStyle截图证明

实际上，使用defaultView基本上是没有必要的，getComputedStyle本身就存在window对象之中。根据DennisHall的说法，使用defaultView可能一是人们不太乐意在window上专门写个东西，二是让API在Java中也可用（这我不懂，忘指点~~）。

不过有个特殊情况，在FireFox3.6上不使用defaultView方法就搞不定的，就是访问框架(frame)的样式.

五、getComputedStyle兼容性
-
- 对于桌面设备：

 属性|Chrome|Firefox(Gecko)|	Internet Explorer|	Opera|	Safari
---|---|---|---|---|---
基本支持|支持	|支持| 9 |	支持|	支持
伪类元素支持	|支持	|支持	|不支持	|不支持|	支持
- 对于手机设备：

属性 |Android	|Firefox Mobile (Gecko)|	IE Mobile|	Opera Mobile|	Safari Mobile
---|---|---|---|---|---|---
基本支持	|支持	|支持	WP7 |Mango	|支持	|支持
伪元素支持	|?|	?	|不支持|	?	|?

上面打问号的表示没有测试，是否兼容不知。如果您方便测试，欢迎将测试结果告知，这里将及时更新，并附上您的姓名，以谢您做的贡献。

我们先把注意力放在桌面设备上，可以看到，getComputedStyle方法IE6~8是不支持的，得，背了半天的媳妇，发现是孙悟空变的——郁闷了。不急，IE自有自己的一套东西。

六、getComputedStyle与currentStyle
-
currentStyle是IE浏览器自娱自乐的一个属性，其与element.style可以说是近亲，至少在使用形式上类似，element.currentStyle，差别在于element.currentStyle返回的是元素当前应用的最终CSS属性值（包括外链CSS文件，页面中嵌入的`<style>`属性等）。

因此，从作用上将，getComputedStyle方法与currentStyle属性走的很近，形式上则style与currentStyle走的近。不过，currentStyle属性貌似不支持伪类样式获取，这是与getComputedStyle方法的差异，也是jQuery css()方法无法体现的一点。

//zxx: 如果你只知jQuery css()方法，你是不会知道伪类样式也是可以获取的，虽然部分浏览器不支持。

例如，我们要获取一个元素的高度，可以类似下面的代码：
```
alert((element.currentStyle? element.currentStyle : window.getComputedStyle(element, null)).height);
```

结果FireFox下显示24px(经过计算了), 而IE浏览器下则是CSS中的2em属性值：
Firefox下显示的计算后的24px值 张鑫旭-鑫空间-鑫生活 IE9下显示的CSS中的2em值 张鑫旭-鑫空间-鑫生活

getComputedStyle方法与currentStyle属性其他具体差异还有很多，我以一个普通按钮做元素，遍历了其中靠谱的属性名和属性值，您可以狠狠地点击这里：getComputedStyle和currentStyle属性展示demo

仔细对比查看，我们可以看到不少差异，例如浮动属性，FireFox浏览器下是这个(cssFloat)：
FireFox下的浮动属性名 

IE7浏览器下则是styleFloat ：
IE7浏览器下的styleFloat属性 张鑫旭-鑫空间-鑫生活

而IE9浏览器下则是cssFloat和styleFloat都有。

等其他N多差异。

七、getPropertyValue方法
-
getPropertyValue方法可以获取CSS样式申明对象上的属性值（直接属性名称），例如：
```js
window.getComputedStyle(element, null).getPropertyValue("float");
```
如果我们不使用getPropertyValue方法，直接使用键值访问，其实也是可以的。但是，比如这里的的float，如果使用键值访问，则不能直接使用getComputedStyle(element, null).float，而应该是cssFloat与styleFloat，自然需要浏览器判断了，比较折腾！

使用getPropertyValue方法不必可以驼峰书写形式（不支持驼峰写法），例如：style.getPropertyValue("border-top-left-radius");

兼容性
getPropertyValue方法IE9+以及其他现代浏览器都支持，见下表：
属性| Chrome|	Firefox (Gecko)	|Internet Explorer|	Opera	|Safari
---|---|---|---|---|---|---
基本支持	|支持|	支持|	9	|支持|	支持|
OK，一涉及到兼容性问题(IE6-8肿么办)，感觉头开始微微作痛了~~，不急，IE自由一套自己的套路，就是getAttribute方法。

八、getPropertyValue和getAttribute
-
在老的IE浏览器（包括最新的），getAttribute方法提供了与getPropertyValue方法类似的功能，可以访问CSS样式对象的属性。用法与getPropertyValue类似：

style.getAttribute("float");
注意到没，使用getAttribute方法也不需要cssFloat与styleFloat的怪异写法与兼容性处理。不过，还是有一点差异的，就是属性名需要驼峰写法，如下：

style.getAttribute("backgroundColor");
如果不考虑IE6浏览器，貌似也是可以这么写：

style.getAttribute("background-color");
实例才是王道，您可以狠狠地点击这里：getPropertyValue和getAttribute获取背景色demo

结果FireFox下一如既往的rgb颜色返回(Chrome也是返回rgb颜色)：
FireFox浏览器下一如既往的RGB颜色返回 张鑫旭-鑫空间-鑫生活

对于IE9浏览器，虽然应用的是currentStyle, 但是从结果上来讲，currentStyle返回的对象是完全支持getPropertyValue方法的。


九、getPropertyValue和getPropertyCSSValue
-
从长相上看getPropertyCSSValue与getPropertyValue是近亲，但实际上，getPropertyCSSValue要顽劣的多。

getPropertyCSSValue方法返回一个CSS最初值(CSSPrimitiveValue)对象(width, height, left, …)或CSS值列表(CSSValueList)对象(backgroundColor, fontSize, …)，这取决于style属性值的类型。在某些特别的style属性下，其返回的是自定义对象。该自定义对象继承于CSSValue对象（就是上面所说的getComputedStyle以及currentStyle返回对象）。

getPropertyCSSValue方法兼容性不好，IE9浏览器不支持，Opera浏览器也不支持（实际支持，只是老是抛出异常）。而且，虽然FireFox中，style对象支持getPropertyCSSValue方法，但总是返回null. 因此，目前来讲，getPropertyCSSValue方法可以先不闻不问。