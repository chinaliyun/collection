第一种
-
在组件内部定义样式并调用
```js
class ComponentHeader extends React.Component {
	render() {
		const styleComponentHeader = {
			header: {
				backgroundColor: '#333',
				color: 'red',
				// "padding-top": "20px",//这种写法react会提示警告，要求流使用JSX的模式，即paddingTop
				paddingBottom: '20px'
			}
		};
		return (
			<h1 style={styleComponentHeader.header} >这是头部</h1>
		)
	}
}
```
这种方式的缺点是不适用于css动画场景，且样式不能用于全局其他位置

第二种
-
传统的class样式
```js
//css
.header{
	font-size: 20px;
}

//js
class ComponentHeader extends React.Component {
	render() {
		return (
			<h1 className='header' >这是头部</h1>
		)
	}
}
```
这种方式的优点是定义的样式可以适用于全局的同类名元素，缺点是不能做到按需加载，即只加载当前组件需要的样式文件

**注意：类名必须使用`className`来定义**
第三种
-
使用CSS MODULE，详细情况看本分类下关于css-loader的另一片文章<a href="../webpack/css-loader.md"></a>


参考文章：<a href="http://www.ruanyifeng.com/blog/2016/06/css_modules.html">http://www.ruanyifeng.com/blog/2016/06/css_modules.html</a>