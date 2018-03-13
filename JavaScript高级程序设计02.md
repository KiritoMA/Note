#  面向对象
重点：**原型**
## 创建对象
Javascript中没有类的概念，对象是属性的集合，可以想象成成散列表。
### 对象实例
创建object实例，为它添加方法
```javascript
	var person = new Object();
	person.name = "Yuki";
	person.age = 23;
	person.job = "Student";
	person.sayName = function(){
		alert(this.name);
	};
```
###  对象字面量
其实就是创建object实例的简写形式
```javascript
	var person = {
		name : "Yuki",
		age : 23,
		job : "Student",
		sayName : function(){
			alert(this.name);
		}
	};
	console.log(typeof person);	//object
```
###  工厂模式
```javascript
	function creatPerson(name,age,job){
		var o = new Object();
		o.name = name;
		o.age = age;
		o.job = job;
		return o;
	}
	var person1 = creatPerson("Yuki",23,"Student");
```
###  构造函数模式
```javascript
	function Person(name,age,job){
		this.name = name;
		this.age = age;
		this.job = job;
	}
	var person1 = new Person("Yuki",23,"Student");

	console.log(person1 instanceof Person);	//true
	console.log(person1 instanceof Object);	//true
```
优势：实例`person1`标识为一个特定的类型`Person`。
```javascript
	console.log(person1.constructor);	//Person
```
实例有一个`constructor`属性，指向构造函数`Person`。
###  原型模式

创建的每个函数都有一个`prototype`（原型）属性，这个属性是个指针，指向一个对象（原型对象），这个对象包含所有其实例共享的属性和方法。
```javascript
	function Person(){};	//空的构造函数
	Person.prototype.name = "Yuki";
	Person.prototype.age = 23;
	Person.prototype.job = "Student"
	Person.prototype.sayName = function(){
		alert(this.name);
	};

	var person1 = new Person();
	person1.sayName();	//"Yuki"

	var person2 = new Person();
	person2.sayName();	//"Yuki"

	alert(person1.sayName == person2.sayName);	//true
```
下面给出对象属性之间的关系，`-->`表示指针指向。
* `Person.prototype`-->`原型对象`（⚠️不是构造函数）
*  `Person.prototype.constructor`-->`Person`
*  `person1.prototype`-->`Person.prototype`
>  `constructor`属性，是原型自带的属性。

当读取某个对象的属性时，首先搜索实例本身是否有给定名字的属性。
如果有，返回该属性，如果没有，搜索指针指向的原型对象，从原型对象中找。

如果在实例中添加了和原型中同名的属性，则程序会读取实例创建的属性（优先搜索到实例的属性，实例原型中的属性也在，只是不会被搜索）
```javascript
	person1.name = "js";
	person1.sayName();	//"js"

	person1.name = null;
	person1.sayName();	//null

	delete person1.name;
	person1.sayName();	//"Yuki" 来自原型

	person2.sayName();	//"Yuki"
```
如果实例中添加同名属性，只能用`delete`操作符完全删除这个属性，才可以重新访问原型中的属性

`Object.keys()`用于访问所有可枚举的实例属性，返回一个`Array`
由此可见，`Object`非常像一个散列表

```javascript
	var keys = Object.keys(Person.prototype);
	console.log(keys);	//["name","age","job","sayname"]

	var p1 = new Person();
	console.log(Object.keys(p1));	//[]
```

####  更简单的原型语法
用**对象字面量**重写原型对象，减少不必要的输入
```javascript
	function Person(){}
	Person.prototype = {
		name : "Yuki",
		age : 23,
		job : "Student",
		sayName : function(){
			alert(this.name);
		}
	};

	var friend = new Person();

	console.log(friend instanceof Object);	//true
	console.log(friend instanceof Person);	//true
	console.log(friend.constructor);	//f Object(){[native code]}
	console.log(Person.prototype);	//{constructor: ƒ, name: "Yuki", age: 23, job: "Student", sayName: ƒ}
```
这里`constructor`指向`Object()`而不再是`Person`了。在后文提到的**构造函数+原型模式**中，需要`constructor`指向构造函数。
如果需要指向`Person`，用如下方法设置：
```javascript
	function Person(){}
	Person.prototype = {
		constructor : Person,
		name : "Yuki",
		age : 23,
		job : "Student",
		sayName : function(){
			alert(this.name);
		}
	};
```
对原型的任何修改会在实例中反应出来：
```javascript
	var friend = new Person();
	Person.prototype.sayHi = function(){alert("hi")};
	friend.sayHi();	//"hi"
```
实际上就是之前所说的一个搜索的过程，先搜索实例中是否有`sayHi`属性，没有则在原型中找。

重写整个原型对象会出错
```javascript
	function Person(){}
	friend = new Person();

	Person.prototype = {
		constructor : Person,
		name : "Yuki",
		age : 23,
		job : "Student",
		sayName : function(){
			alert(this.name);
		}
	};

	friend.sayName();	//error
```
重写原型对象之前：
`friend.prototype`-->`Person Prototype`
`Person.prototype`-->`Person Prototype`原型对象
`Person Prototype.constructor`-->`Person`
重写原型对象之后：
`friend.prototype`-->`Person Prototype`
`Person.prototype`-->`New Person Prototype`新原型对象
`Person Prototype.constructor`-->`Person`
`New Person Prototype.constructor`-->`Person`
理解如下例子：
```javascript
	function Person(){}
	Person.prototype = {
		constructor : Person,
		name : "Yuki",
		age : 23,
		job : "Student",
		sayName : function(){
			alert(this.name);
		}
	};
	Person.prototype = {
		name : "JS"
	};
	console.log(Person.prototype);	//{name: "JS"}
```

一切皆对象，原生引用类型也是用这种方式创建的
```javascript
	console.log(Array.prototype);	//[constructor: ƒ, concat: ƒ, pop: ƒ, push: ƒ, shift: ƒ, …]
	console.log(String.prototype);	//String {"", length: 0, constructor: ƒ, anchor: ƒ, big: ƒ, blink: ƒ, …}
```
`构造函数.prototype` --> `原型对象`
注意：不推荐修改原生对象的原型

原型模式存在的问题，见下例：
```javascript
	function Person(){}
	Person.prototype = {
		constructor : Person,
		name : "Yuki",
		age : 23,
		job : "Student",
		skills : ["js","vba"],
		sayName : function(){
			alert(this.name);
		}
	};
	var person1 = new Person();
	var person2 = new Person();
	person1.skills.push("van");

	console.log(person1.skills);	//["js", "vba", "van"]
	console.log(person2.skills);	//["js", "vba", "van"]
```
上述代码，只有`person.skill`会产生问题，因为`skill`的值是数组（引用类型）。

###  构造函数+原型模式
> 创建自定义类型最常见的方式
构造函数模式：定义实例属性
原型模式：定义方法和共享的属性

```javascript
	function Person(name,age,job){
		this.name = name;
		this.age = age;
		this.job = job;
		this.skills = ["js","vba"]
	}

	Person.prototype = {
		constructor : Person,
		sayName : function(){
			alert(this.name);
		}
	};
	var person1 = new Person("Yuki",23,"Student");
	var person2 = new Person("Kirito",23,"Student");

	person1.skills.push("c");
	console.log(person1.skills);	//["js", "vba", "c"]
	console.log(person2.skills);	//["js", "vba"]
```
其他一些创建对象的方式：
*  动态原型模式
*  寄生构造函数模式
*  稳妥构造函数模式

## 继承
ECMAScript只支持实现继承，主要依靠原型链来实现。
###原型链
之前讲过，原型、构造函数、实例的关系如下：
`实例.prototype`-->`原型对象`
`构造函数.prototype`-->`原型对象`
`原型对象.constructor`-->`构造函数`
如果让`原型对象`等于`另一个实例`，那么`此原型对象`将包含一个指向`另一个原型`的指针。
```javascript
	function SuperType(){
		this.property = true;
	}
	SuperType.prototype.getSuperValue = function(){
		return this.property;
	};
	function SubType(){
		this.subproperty = false;
	}
	SubType.prototype = new SuperType();

	SubType.prototype.getSubValue = function(){
		return this.subproperty;
	};

	var instance = new SubType();

	console.log(instance.getSuperValue());	//true
	console.log(instance instanceof Object);	//true
	console.log(instance instanceof SubType);	//true
	console.log(instance instanceof SuperType);	//true
```
实现继承的本质是重写原型对象，这里用了`new Supertype()`新类型的实例取代原型对象。
![继承关系图](http://upload-images.jianshu.io/upload_images/9445448-00874839031044a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
没有用到`subType`默认的原型，而是以`superType`的实例替换。
创建了原型链：`instance`-->`SubType Prototype`-->`SuperType Prototype`
注意：`instance.constructor`指向`SuperType`。
访问实例属性时，沿原型链向上搜索:
1.  实例搜索
2.  搜索`SubType.prototype`
1.  搜索`SuperType.prototype`

实际上，所有函数的默认原型都是`Object`的实例，默认原型都会包含一个内部指针，指向`Object.prototype`。
![完整的原型链](http://upload-images.jianshu.io/upload_images/9445448-b9411e252cefc218.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

同原型模式一样存在问题，包含引用类型值的原型属性会被所有实例共享。而且还存在不能向`SuperType`的构造函数中传递参数的问题。

```javascript
	function SuperType(){
		this.colors = ["red","blue","green"];
	}
	function SubType(){}
	SubType.prototype = new SuperType();

	var instance1 = new SubType();
	instance1.colors.push("black");
	console.log(instance1.colors);

	var instance2 = new SubType();	//["red", "blue", "green", "black"]
	console.log(instance2.colors);	//["red", "blue", "green", "black"]
```
###  借用构造函数
```javascript
	function SuperType(){
		this.colors = ["red","blue","green"];
	}
	function SubType(){
		SuperType.call(this);
	}
	var instance1 = new SubType();
	console.log(instance1);	//{colors : ["red", "blue", "green"]}
	instance1.colors.push("black");
	console.log(instance1.colors);	//["red", "blue", "green", "black"]

	var instance2 = new SubType();
	console.log(instance2.colors);	//["red", "blue", "green"]
```
借用构造函数不仅可以修改实例属性，还能改在`子类型`构造函数中向`父类型`构造函数传递参数。

```javascript
	function SuperType(name){
		this.name = name;
	}
	function SubType(){
		SuperType.call(this,"Yuki");
		this.age = 23;
	}
	var instance = new SubType();
	console.log(instance.name);	//"Yuki"
	console.log(instance.age);	//23
```
但是此方法无法**复用**，所以引入组合继承。

###组合继承
用构造函数继承属性，用原型链继承方法
```javascript
	function SuperType(name){
		this.name = name;
		this.colors = ["red","blue","green"];
	}
	SuperType.prototype.sayName = function(){
		alert(this.name);
	};
	function SubType(name,age){
		//继承属性
		SuperType.call(this,name);
		this.age = age;
	}
	//继承方法
	SubType.prototype = new SuperType();
	console.log(SubType.prototype.constructor);	//SuperType
	SubType.prototype.constructor = SubType;
	SubType.prototype.sayAge = function(){
		alert(this.age);
	};

	var instance1 = new SubType("Yuki",23);
	instance1.colors.push("black");
	console.log(instance1);	//SubType {name: "Yuki", colors: Array(4), age: 23}
	instance1.sayName();
	instance1.sayAge();

	var instance2 = new SubType("JS",23);
	console.log(instance2);	//SubType {name: "JS", colors: Array(3), age: 23}
```
这样每个新实例可以拥有自己的属性，又可以使用新的方法。

其他一些继承方式：

*  原型式继承
*  寄生式继承
*  寄生组合式继承

***
>  参考 [MDN web docs](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Functions)
>  《JavaScript高级程序设计》
