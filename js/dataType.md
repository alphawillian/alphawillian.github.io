## js的数据类型主要分为值类型和引用数据类型。值类型有number，string,boolean和undefined 引用类型是：Array，Object，Function和null。接下来我们用几种js方法来判断这些数据类型。
我们先来定义几个变量。

```js
	var num = 100;
	var str = "string";
	var bool = true;
	var un = undefined;
	var nu = null;
	var arr = ["tom","jerry"];
	var obj = {
		kind:"dog",
		yell:"wang"
	};
	var fn = function(){
		console.log("I'm a function");
	}
```
1.用typeof来判断
```js
	console.log(typeof num);    	//number
	console.log(typeof str);	//string
	console.log(typeof bool);	//boolean
	console.log(typeof un);	        //undefined
	console.log(typeof nu);		//object
	console.log(typeof arr);	//object
	console.log(typeof obj);	//object
	console.log(typeof fn);	    	//function
```
可以看出来值类型的数据可以用typeof来判断，引用类型的function类型也可以用typeofp来判断。但是引用类型的数据null,Array,Object返回的都是object。所以就需要第二种方法-->instanceof。

2.在引用数据类型的基础上我们用instanceof来判断
```js
	console.log(arr instanceof Array);    	 //true
	console.log(obj instanceof Object);	//true
	console.log(fn instanceof Function);   	//true
```
我们需要注意值类型的数据和null不能用instanceof来判断，不止会判断不准确甚至还会程序报错。错误代码如下：
```js
	console.log(num instanceof Number);    	//false
	console.log(str instanceof String);	//false
	console.log(bool instanceof Boolean);	//false
	console.log(un instanceof undefined);	//Uncaught TypeError: Right-hand side of 'instanceof' is not an object
	console.log(nu instanceof null);	//Uncaught TypeError: Right-hand side of 'instanceof' is not an object
```
用typeof来判断值类型数据用instanceof来判断引用类型数据，难道就没有一种方法既能判断值类型又能判断引用类型吗？有，那就是constructor方法。

3.数据类型用constructor来判断
```js
	console.log(num.constructor === Number);	//true
	console.log(str.constructor === String);  	//true
	console.log(bool.constructor === Boolean);	//true
	console.log(arr.constructor === Array);	   	//true
	console.log(obj.constructor === Object);  	//true
	console.log(fn.constructor === Function);	//true
```
我们需要注意的是null,undefined用constructor会报错。
```js
	console.log(nu.constructor === null);    //Uncaught TypeError: Cannot read property 'constructor' of null
	console.log(un.constructor === undefined);	//Uncaught TypeError: Cannot read property 'constructor' of null
```
好不容易找到一个既能判断值类型又能判断引用类型的函数，但是又不能用于null和undefined。难道没有更好的函数吗？有，那就是我们的第四个函数Object.prototype.toString.call()；

4.更好用的数据类型判断函数 Object.prototype.toString.call()。
```js
	console.log(Object.prototype.toString.call(null));    	 //[object Null]
	console.log(Object.prototype.toString.call(undefined));//[object Undefined]
	console.log(Object.prototype.toString.call(nu) === '[object Null]');   //true
	console.log(Object.prototype.toString.call(un) === '[object Undefined]');//true
	console.log(Object.prototype.toString.call(num) === '[object Number]');//true
	console.log(Object.prototype.toString.call(str) === '[object String]');//true
	console.log(Object.prototype.toString.call(bool) === '[object Boolean]');//true
	console.log(Object.prototype.toString.call(arr) === '[object Array]');//true
	console.log(Object.prototype.toString.call(obj) === '[object Object]');//true
	console.log(Object.prototype.toString.call(fn) === '[object Function]');//true
```
上面的四个方法就能判断数据类型了，需要我们注意的是instanceof,constructor, Object.prototype.toString.call()。还可用于判断其他是数据类型例如：
```js
	var date = new Date();
	var reg = /^a+b+$/;
	console.log(date instanceof Date);     	//true
	console.log(reg instanceof RegExp);	//true
	console.log(date.constructor === Date);    //true
	console.log(reg.constructor === RegExp);   //true
	console.log(Object.prototype.toString.call(date));	//[object Date]
	console.log(Object.prototype.toString.call(reg));	//[object RegExp]
    
```