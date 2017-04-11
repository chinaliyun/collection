第一种方式，
-
就是平常的给DOM指定一个ID，通过正常的方式去获取DOM，然后去操作DOM，但是这种方法是不被推荐的

```js
class A extends React.Component{
	change(){
		var submitButton = document.querySelector('#submitButton');
		ReactDOM.findDOM(submitButon).style.color = 'red';
	}
	render(){
		return (
			<button id="submitButton" onClick={this.change.bind(this)}></button>
		)
	}
}
```
第二种方式
-
通过react的refs属性： **refs是访问组件内部DOM节点唯一可靠的方法**，
```js
class A extends React.Component{
	change(){
		this.refs.submitButton.style.color = 'red';
	}
	render(){
		return (
			<button ref="submitButton" onClick={this.change.bind(this)}></button>
		)
	}
}
```
需要注意的是，由于 this.refs.[refName] 属性获取的是真实 DOM ，所以必须等到虚拟 DOM 插入文档以后，才能使用这个属性，否则会报错。上面代码中，通过为组件指定 Click 事件的回调函数，确保了只有等到真实 DOM 发生 Click 事件之后，才会读取 this.refs.[refName] 属性。

也就是说，不要在render中或者render方法之前调用ref，否则会报错

那么如何确保react执行到了在render后：  componentDidMount !!!!!!!千万千万千万不要滥用refs，尽可能通过state和props去更新view层
