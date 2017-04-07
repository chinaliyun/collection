当我们在写HTML的时候，出现语法错误的时候很难察觉。这很容易导致代码里面有 无效的，诡异的僵尸代码，而且很不明显。

有很多方法来检查我们的HTML代码，发现并解决错误，比如：使用 W3C标签校验服务 。 另外我们也有简单的方法并且能将其整合到我们的工作流当中来，那就是：使用一些稍微高级的CSS选择器来高亮潜在问题区域。有一些简单的错误我们可以用CSS选择器来捕捉到他。

行内样式
-
```css
*[style] { 
    border: 5px solid red; /* Style to make the elements noticeable */
}
```
这个选择器将标记处页面上所有写了行内样式的标签。由于行内样式权重很高所以很难覆盖它，通常我们需要避免写行内样式。尽管有时候我们必须要使用行内样式，这个方法也可以将它们高亮，作为个别案例评估标准。

当用这个办法选中了问题元素后我们可以写任意的样式来让它们更加明显。

A标签里面的链接非法
-
```css
a:not([href])
a[href="#"]
a[href=""]
a[href*="javascript:void(0)"] { …    
```
上述选择器将标记出A标签，无href, 活着有一个无效的href.
不可访问的图片Img标签
-
```css
img:not([alt]) { ... }
```
作为一个笼统的规则，所有的图片应该有alt属性。如果没有这个属性很多屏幕大多数屏幕阅读器将会读取img的src属性，这个信息对于用户来说是无用的，混淆的。

还有一点应该注意，上述的选择器将不会选取带有空值的alt属性，例如 `<img src="photo.png" alt="">`。 因为空值有可能是开发者蓄意让屏幕阅读器跳过这个img标签, 比如这个图片是纯装饰性的。但是将空值的标记出来，有时候也是需要的。

```css
img[alt=""] { ... }
```
缺少文档语言
-

```css
html:not([lang])
html[lang=""] { ... }
```

所有的html标签都应该写明的属性就是[lang]属性。这个属性将会告诉屏幕阅读器这个网页用的是种语言，以使阅读器选择那种语言来朗读。

如果没有这个标签会发生啥呢？来看个视频：

分享自 @HTeuMeuLeu’s 为什么设置 (eg. lang=”en”) 这么重要. 😂 https://t.co/tjn8GvPVKM

错误的编码
-
```css
meta[charset]:not([charset="UTF-8"]) { ... }
```
这个选择器将选中任何一个没有设置UTF-8属性的meta编码标签。这个标签是高速浏览器使用UTF-8来编码。UTF-8是目前推荐的HTML文档编码格式，W3C的valid HTML要求必须标明编码格式。
理想情况下这个标签需要放在<head>里面第一个位置。我们同样可以检测出来。

```css
meta[charset="UTF-8"]:not(:first-child) { ... }
```
无法读取的视口属性
-
```css
meta[name="viewport"][content*="user-scalable=no"]
meta[name="viewport"][content*="maximum-scale"]
meta[name="viewport"][content*="minimum-scale"] { ... }
```
这个选择器将标记处无法读取的视口属性。

没有label包围的表单
-
```css
input:not([id])
select:not([id])
textarea:not([id]) { ... }

label:not([for]) { ... }
```
当谈到加标注表单元素应该是最重要的元素。尽管有很多方式来给表单元素加标注，最常用的方式是在指代该表单元素的label元素上添加ID。上面的选择器是检测label上面没有添加ID并且label元素没有明确用ID指代表单元素。

另外一种方式是用过name来标记表单元素。ID属性被用来标明该元素在HTML文档中的上下文的，name 属性则是来指代将要被form data 提交的元素。

```css
input:not([name]), 
select:not([name])
textarea:not([name]) { ... }
```
另外我们也可以要求给form元素添加 name 活着ID。
```css
form:not([name]):not([id]) { ... }
```
空的交互式元素
-
```css
button:empty
a:empty { ... }
```
交互式元素，例如链接、按钮是典型的被它的内容标记的元素。尽管有别的方式来标记这个元素，比如aria-label属性，如果这些元素内容为空好是一个错误的信号。

没有必要／废弃属性
-
```css
script[type="text/javascript"]
link[rel="stylesheet"][type="text/css"] { ... 
```
