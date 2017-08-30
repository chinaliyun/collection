```
class Person{
	static age = 16;
	// 相当于Person.age=16,这种生命不会被Person生成的实例对象访问到

	name = 'zhangsan';
	// 相当于this.name ='zhangsan'

	getName(){
		return this.name
	}
	getName  = function(){return this.name}
	// 这两种方法将被添加到Person的原型链中，允许被Person生成的实例对象访问，因此react中调用这中方法的时候，千万要小心使用this，如果要使用this，一定要记得指定上下文
	
	getAge = ()=>this.age
	//这种方法相当于this.getAge = function(){return this.age},既不用担心里面的this指向，也可以被Person生成的实例对象直接调用

}
```

