#	函数基础
>  函数本质：特定环境中（调用时的环境）执行代码的对象
## 嵌套函数
1. 内部函数只可以在外部函数中访问。
1. 内部函数形成了一个闭包：它可以访问外部函数的参数和变量，但是外部函数却不能使用它的参数和变量。

```javascript

function outside(x) {
  function inside(y) {
    return x + y;
  }
  return inside;
}
fn_inside = outside(3); // Think of it like: give me a function that adds 3 to whatever you give it
result = fn_inside(5); // returns 8
result1 = outside(3)(5); // returns 8

```
这里内部函数`inside()`，可以访问外部函数`outside()`里的变量x。
而在外部只能调用`outside()`。
第一次调用时，可以理解为`outside(3) = 3 + y`。
## 多层嵌套函数
```javascript
function A(x) {
  function B(y) {
    function C(z) {
      console.log(x + y + z);
    }
    C(3);
  }
  B(2);
}
A(1); // logs 6 (1 + 2 + 3)
```
**这里的作用域链是（C，B，A，全局/windows）**

1. B形成了一个包含A的闭包，B可以访问A的参数和变量
1. C形成了一个包含B的闭包
1. B包含A，所以C也包含A，C可以访问B和A的参数和变量。换言之，C用这个顺序链接了B和A的作用域。


## 函数内部属性

### argument.callee

`arguments`有`callee`属性，是个指针，指向 这个`arguments`对象的函数

```javascript
		function factorial(num){
			if (num <= 1) {
				return 1
			} else {
				return num * arguments.callee(num-1)
			}
		}

		var trueFactorial = factorial;
		factorial = function(){
			return 0;
		};
		console.log(trueFactorial(5));//120
		console.log(factorial(5));//0
```  
需要理解的是，所有的函数都在一个堆栈中，标识符只是一个指向堆栈中函数的指针。这里`trueFactorial`保存了一开始`factorial`函数的指针。

如果不采用`return num * arguments.callee(num-1)`，而改为平常经常使用的`return num * factorial(num-1)`，则有：
```javascript
		console.log(trueFactorial(5));//0
		console.log(factorial(5));//0
```
### this
`this`引用函数执行时的环境对象。
```javascript
		window.color = "red";
		var o = {color: "blue"};
		function sayColor(){
			alert(this.color)
		}
		sayColor();  //"red"
		o.sayColor = sayColor;
		o.sayColor();//"blue"
```
`sayColor()`中，`this`引用全局对象`window`（ 这里`this.color`其实就是`window.color`）。
 `o.sayColor()`中，`this`引用对象`o`。

## 函数属性和方法

ES中，函数是对象。因此函数也有属性和方法。

### length

`length`返回期望收到的**命名参数**的个数。
```javascript
		function sum(num1,num2){
			return num1+num2;
		}
		console.log(sum.length);//2
```
### apply

`apply()`方法接收两个参数：1. 运行函数的作用域；2. 参数数组，可以是`Array`实例，可以是`arguments`。

```javascript
		function sum(num1,num2){
			return num1 + num2;
		}
		function callSum1(num1,num2){
			return sum.apply(this,arguments);
		}
		function callSum2(num1,num2){
			return sum.apply(this,[num1,num2]);
		}
		console.log(callSum1(10,10));//20
		console.log(callSum2(10,10));//20
```
在`callSum1()`中传入`this`作为`this`值，这里`this`是全局作用域。
可以不写命名参数，像这样`function callSum1()`。

### call

`call()`与`apply()`类似，`call()`传递给函数的参数必须逐个列举。

```javascript
		function sum(num1,num2){
			return num1 + num2;
		}
		function callSum1(num1,num2){
			return sum.call(this,num1,num2)
		}
		console.log(callSum1(10,10));//20
```
`callSum1()`必须明确传入每一个参数，写成`function callSum1()`会报错。

实际上经常用`call()`和`apply()`扩充或改变作用域：
```javascript
		window.color = "red";
		var o = {color: "blue"};
		function sayColor(){
			alert(this.color)
		}
		sayColor();  //red
		sayColor.call(this);  //red
		sayColor.call(window);  //red
		sayColor.call(o);  //blue
```

### bind

`bind()`会创建一个函数实例，`this`直接绑定`bind()`的参数。
```javascript
	window.color = "red";
	var o = {color : "blue"};
	function sayColor(){
		alert(this.color);
	}
	var objectSayColor = sayColor.bind(o);
	objectSayColor();	//blu
```
## 基本包装类型

`Boolean`,`Number`,`String`
>  这里不详细讲了，要记住javascript中一切皆对象。基本包装类型的对象生存期很短。

### Global对象

所有在全局作用域中定义的属性和函数，都是Global对象的属性。

#### URI编码方法

`encodeURI()`,`encodeURIComponent()`
#### eval()

`eval()`相当于一个解析器，下面两行代码等价。
```javascript
	eval("alert('hi');");
	alert('hi');
```
注意下面的情况：
```javascript
	function sum(num1,num2){return num1+num2}
	alert(sum(2,3));	//6
	function sum(num1,num2){return num1*num2}
```
这里`sum()`执行乘法，因为javascript中是先加载所有定义的函数，再一步步执行代码。

```javascript
	function sum(num1,num2){return num1+num2}
	alert(sum(2,3));	//5
	eval("function sum(num1,num2){return num1*num2}");
```
这里`sum()`执行加法，因为`eval()`创建的函数或变量不会提升，只在`eval()`执行时创建。

下表列出`Global`对象的所有属性

属性         | 说明           | 
---------------|------------------|
undefined | 特殊值  |
NaN       | 特殊值   |
Infinity  | 特殊值      | 
Object      | 构造函数|
Array           |构造函数  |
Function       | 构造函数 | 
Boolean           | 构造函数|
String              | 构造函数|
Number          | 构造函数|
Date          | 构造函数|
RegExp          | 构造函数|
Error | 构造函数|
EvalError| 构造函数|
RangeError| 构造函数|
ReferenceError| 构造函数|
SyntaxError| 构造函数|
TypeError| 构造函数|
URLError| 构造函数|


***
>  参考 [MDN web docs](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Functions)
>  《JavaScript高级程序设计》
