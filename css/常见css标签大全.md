一、 CSS文字属性：
-
color : #999999; /*文字颜色*/

font-family : 宋体,sans-serif; /*文字字体*/

font-size : 9pt; /*文字大小*/

font-style:itelic; /*文字斜体*/

font-variant:small-caps; /*小字体*/

letter-spacing : 1pt; /*字间距离*/

line-height : 200%; /*设置行高*/

font-weight:bold; /*文字粗体*/

vertical-align:sub; /*下标字*/

vertical-align:super; /*上标字*/

text-decoration:line-through; /*加删除线*/

text-decoration: overline; /*加顶线*/

text-decoration:underline; /*加下划线*/

text-decoration:none; /*删除链接下划线*/

text-transform : capitalize; /*首字大写*/

text-transform : uppercase; /*英文大写*/

text-transform : lowercase; /*英文小写*/

text-align:right; /*文字右对齐*/

text-align:left; /*文字左对齐*/

text-align:center; /*文字居中对齐*/

text-align:justify; /*文字分散对齐*/

vertical-align属性

vertical-align:top; /*垂直向上对齐*/

vertical-align:bottom; /*垂直向下对齐*/

vertical-align:middle; /*垂直居中对齐*/

vertical-align:text-top; /*文字垂直向上对齐*/

vertical-align:text-bottom; /*文字垂直向下对齐*/

二、CSS边框空白
-
padding-top:10px; /*上边框留空白*/

padding-right:10px; /*右边框留空白*/

padding-bottom:10px; /*下边框留空白*/

padding-left:10px; /*左边框留空白*/

三、CSS符号属性：
-
list-style-type:none; /*不编号*/

list-style-type:decimal; /*阿拉伯数字*/

list-style-type:lower-roman; /*小写罗马数字*/

list-style-type:upper-roman; /*大写罗马数字*/

list-style-type:lower-alpha; /*小写英文字母*/

list-style-type:upper-alpha; /*大写英文字母*/

list-style-type:disc; /*实心圆形符号*/

list-style-type:circle; /*空心圆形符号*/

list-style-type:square; /*实心方形符号*/

list-style-image:url(/dot.gif); /*图片式符号*/

list-style-position: outside; /*凸排*/

list-style-position:inside; /*缩进*/

四、CSS背景样式：
-
background-color:#F5E2EC; /*背景颜色*/

background:transparent; /*透视背景*/

background-image : url(/image/bg.gif); /*背景图片*/

background-attachment : fixed; /*浮水印固定背景*/

background-repeat : repeat; /*重复排列-网页默认*/

background-repeat : no-repeat; /*不重复排列*/

background-repeat : repeat-x; /*在x轴重复排列*/

background-repeat : repeat-y; /*在y轴重复排列*/

指定背景位置

background-position : 90% 90%; /*背景图片x与y轴的位置*/

background-position : top; /*向上对齐*/

background-position : buttom; /*向下对齐*/

background-position : left; /*向左对齐*/

background-position : right; /*向右对齐*/

background-position : center; /*居中对齐*/

五、CSS连接属性：
-
a /*所有超链接*/

a:link /*超链接文字格式*/

a:visited /*浏览过的链接文字格式*/

a:active /*按下链接的格式*/

a:hover /*鼠标转到链接*/

鼠标光标样式：

链接手指 CURSOR: hand

十字体 cursor:crosshair

箭头朝下 cursor:s-resize

十字箭头 cursor:move

箭头朝右 cursor:move

加一问号 cursor:help

箭头朝左 cursor:w-resize

箭头朝上 cursor:n-resize

箭头朝右上 cursor:ne-resize

箭头朝左上 cursor:nw-resize

文字I型 cursor:text

箭头斜右下 cursor:se-resize

箭头斜左下 cursor:sw-resize

漏斗 cursor:wait

光标图案(IE6)   p {cursor:url(“光标文件名.cur”),text;}

六、CSS框线一览表：
-
border-top : 1px solid #6699cc; /*上框线*/

border-bottom : 1px solid #6699cc; /*下框线*/

border-left : 1px solid #6699cc; /*左框线*/

border-right : 1px solid #6699cc; /*右框线*/

以上是建议书写方式,但也可以使用常规的方式 如下:

border-top-color : #369 /*设置上框线top颜色*/

border-top-width :1px /*设置上框线top宽度*/

border-top-style : solid/*设置上框线top样式*/

其他框线样式

solid /*实线框*/

dotted /*虚线框*/

double /*双线框*/

groove /*立体内凸框*/

ridge /*立体浮雕框*/

inset /*凹框*/

outset /*凸框*/

七、CSS表单运用：
-
文字方块

按钮

复选框

单选按钮

多行文字方块 

下拉式菜单 选项1选项2

选项1

选项2

八、CSS边界样式：
-
margin-top:10px; /*上边界*/

margin-right:10px; /*右边界值*/

margin-bottom:10px; /*下边界值*/

margin-left:10px; /*左边界值*/
