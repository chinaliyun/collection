> 以下单位缓存以宽度为375为基础，可根据设计稿页面自行替换
```less
<!-- 主色调 -->
@dmc: #00b7aa;

.dmc{
	color: @dmc;
}
<!-- 白色背景 -->
.bgw {
    background-color: white;
}
<!-- 线框颜色 -->
@llc: #e0e0e0;
@dlc: #cecece;
@lllc: #eaeaea;

<!-- 字体颜色 -->
.dfc {
    color: #666;
}

.lfc {
    color: #999;
}

.hfc {
    color: #333;
}


<!-- 显示方式 -->
.dib {
    display: inline-block;
}

.db {
    display: block;
}

<!-- 宽高 -->
.wp(@px) {
    width: unit(@px, px);
}
.wr(@px) {
    width: unit(@px/37.5, rem);
}
.h(@px) {
    height: unit(@px, px);
}


<!-- 内边距 -->
.pt(@px) {
    padding-top: unit(@px, px);
}

.pb(@px) {
    padding-bottom: unit(@px, px);
}

.pl(@px) {
    padding-left: unit(@px/37.5, rem);
}

.pr(@px) {
    padding-right: unit(@px/37.5, rem);
}


<!-- 外边距 -->
.mt(@px) {
    margin-top: unit(@px/37.5, rem);
}

.mb(@px) {
    margin-bottom: unit(@px/37.5, rem);
}

.ml(@px) {
    margin-left: unit(@px/37.5, rem);
}

.mr(@px) {
    margin-right: unit(@px/37.5, rem);
}

<!-- 内联元素 -->
.fs0 {
    font-size: 0;
    >* {
        .dib;
    }
}

<!-- 字号 -->
.fs(@px) {
    font-size: unit(@px, px);
    ;
}

<!-- 字体横向对齐方式 -->
.tac {
    text-align: center;
}
.tar {
    text-align: right;
}

.taj {
    text-align: justify;
}

<!-- 字体纵向对齐方式 -->
.vam {
    vertical-align: middle;
}

.vat {
    vertical-align: top;
}

.vab {
    vertical-align: bottom;
}

<!-- 字体缩进 -->
.ti(@px) {
    text-indent: unit(@px/37.5, rem);
}

<!-- 行高 -->

.lh(@px) {
    line-height: unit(@px, px);
}

<!-- 定位 -->
.ptr {
    position: relative;
}

.pta {
    position: absolute;
}

.t(@px) {
    top: unit(@px, px);
}

.b(@px) {
    bottom: unit(@px, px);
}

.l(@px) {
    left: unit(@px/37.5, rem);
}

.r(@px) {
    right: unit(@px/37.5, rem);
}
```