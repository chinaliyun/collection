常用的移动端预处理样式
```less
.br{
    border-radius: 5px;
}
.pm{
    .pl(20);
    .pr(20);
}
.fs(@px) {
    font-size: unit(@px /37.5, rem);
}

.w(@px) {
    width: unit(@px /37.5, rem);
}

.h(@px) {
    height: unit(@px /37.5, rem);
}

.lh(@px) {
    line-height: unit(@px /37.5, rem);
}

.t(@px) {
    top: unit(@px /37.5, rem)
}

.l(@px) {
    left: unit(@px /37.5, rem)
}

.r(@px) {
    right: unit(@px /37.5, rem)
}

.b(@px) {
    bottom: unit(@px /37.5, rem)
}

.fl {
    float: left;
}

.fr {
    float: right;
}

.dib {
    display: inline-block;
}

.db {
    display: block;
}
.dn{
    display: none;
}

.ptr {
    position: relative;
}

.pta {
    position: absolute;
}

.ptf {
    position: fixed;
}

.tl {
    text-align: left;
}

.tc {
    text-align: center;
}

.tr {
    text-align: right;
}

.vt {
    vertical-align: top;
}

.vm {
    vertical-align: middle;
}

.vb {
    vertical-align: bottom;
}

.mt(@px) {
    margin-top: unit(@px / 37.5, rem)
}

.mr(@px) {
    margin-right: unit(@px / 37.5, rem)
}

.mb(@px) {
    margin-bottom: unit(@px / 37.5, rem)
}

.ml(@px) {
    margin-left: unit(@px / 37.5, rem)
}

.pt(@px) {
    padding-top: unit(@px / 37.5, rem)
}

.pr(@px) {
    padding-right: unit(@px / 37.5, rem)
}

.pb(@px) {
    padding-bottom: unit(@px / 37.5, rem)
}

.pl(@px) {
    padding-left: unit(@px / 37.5, rem)
}

.arrowLeft {
    .w(10);
    .h(10);
    border: 2px solid white;
    border-right-color: transparent;
    border-bottom-color: transparent;
    transform: rotate(-45deg);
}
.arrowRight {
    .w(10);
    .h(10);
    .db;
    border: 2px solid @defaultLineColor;
    border-left-color: transparent;
    border-bottom-color: transparent;
    transform: rotate(45deg)
}
```