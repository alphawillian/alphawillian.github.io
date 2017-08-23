# JS 高级（函数、作用域、闭包、this、垃圾回收）

## JS 函数

**函数分为两类具名函数、匿名函数，其变型可以包括自执行函数、递归函数**

1. 具名函数

   含有名字的函数

   ```javascript
   function say(){//...}定义了一个名称为 say 的函数
   say();//调用名称为 say 的函数
   var say = function(){//...}
   var say1 = function say(){//...}//不建议这样写
   ```

2. 匿名函数

   不含名字的函数

   ```javascript
   setTimeout(function(){
      
   },100);
   li.onclick = function(){}
   ```

3. 自执行函数

   创建即执行的函数，可以创建块级作用域

   ```javascript
   (function(){
     //..
   })();
   ```

4. 递归函数

   自己在某些条件下调用自己的函数

   ```javascript
   function calc(num){
     if(num<1){
       return 1;
     }else{
       return num*calc(num-1);
     }
   }
   console.log(calc(4));//24 一个典型的阶乘递归  1*2*3*4
   ```

   ​

## JS 作用域

> 概念：当某个函数被调用时，会创建一个执行环境及相应的作用域（链），该执行环境定论了变量或函数有权访问的其它数据，决定了各自的行为
>
> 每一个执行环境都有一个**变量对象[[Scope]]**，该对象保存着执行环境中的所有变量和函数，我们的代码无法访问这个对象，JS 解析器在处理数据时会使用到它。



### 全局执行环境

在 JS 中，根据宿主的不同，全局执行环境对象也不同，对于 WEB 而言，全局执行环境是 window 对象。

所有的全局变量和函数都是做为 window 对象的属性和方法创建的。

```javascript
var name = "zhar";
function say(){
  console.log(name);
}
console.log(name);//zhar
console.log(window.name);//zhar
say();//zhar
window.say();//zhar
```



### 局部执行环境

每个函数有自己的执行环境。

当执行流进入一个函数时，函数的环境会被推入一个环境栈中。函数执行完成后，栈将环境推出。



### 作用域链

当代码在一个环境中执行时，会创建一个**作用域链对象（scope chain）**。

作用域链的首位，始终是当前执行环境的变量对象。这是一个包含 arguments 和其它命名参数值的活动对象。第二位是外部函数的活动对象，第三位是外部函数的外部函数的活动对象，......，直到作为作用域终点的全局执行环境。

```javascript
var name = "zhar";
function say(){
  var age = 30;
  console.log(name+"-"+age+"-"+address);// 错误：address is not defined
  function info(){
    var address = "北京";
    console.log(name+"-"+age+"-"+address);//zhar-30-北京
  }
  info()
}
say();//
console.log(name+"-"+age+"-"+address);// 错误：age is not defined
```

这段代码共有三个执行环境：全局环境（window）、say()局部环境、info()局部环境。

在全局环境中有一个变量 name 和一个函数 say，在 say 中有一个变量 age 和一个函数 info，在 info 中有一个变量 address。通过报错信息可以看到，say 中可以访问到当前环境中的 age 及全局环境中的 name，info 可以访问到当前环境中的 address、say 环境中的 age、全局环境中的 name，而在 window 环境中则只可以访问到 name。

可以得出：内部环境可以通过作用域链访问到所有的外部环境，但外部环境不能访问内部环境中的任何变量或函数。

作用域链是有层级、线性的；

*JS 在查找变量时，在当前环境查找到变量便停止继续向上搜索*

```javascript
var name = 'zhar';
function say(){
  var name = 'tom';
  console.log(name);//tom
}
say();
console.log(name);//zhar
```



作用域链示意图：

 ![](http://blog.gethin.tech/img/this1.jpg)



### 没有块级作用域

在 JS 中并没有像强类型语言中的块级作用域，比如在 JAVA 中一对花括号中，是一个块级的作用域

```javascript
var name = 'zhar'
if(true){
  var name = 'tom';
}
console.log(name);
//按着上面作用域链中描述的情况，name 应该输出为 zhar，但实际情况为 tom，便是因为在 JS 中没有块级作用域的概念
//另外一种块级作用域的典型情况为 for 循环
for(var i=0;i<5;i++){
  //dosomething
}
console.log(i);//5
```



### 变量提升

```javascript
var name = 'zhar';
function say(){
  console.log(name);//undefined
  var name = 'tom';
}
say();
```

上面的示例代码中就存在着变量提升

1. 声明式

   ```javascript
   say();//运行正常
   function say(){
     console.log("Hello");
   }
   ```

   此为声明式语句，JS 解析器会将声明式语句提升至当前执行环境的顶端

2. 赋值式

   将上面的代码更改如下：

   ```javascript
   say();//运行错误  Uncaught TypeError: say is not a function
   var say = function(){
     console.log("Hello");
   }
   ```

   此为赋值式语句，JS 解析器会将赋值式语句提升到当前执行环境的顶端，并且赋值为 undefined

   更加直接的例子便是该知识点开始的代码





## JS闭包

> 概念：外部函数返回的，持有外部函数局部变量的内部函数
>
> （有权访问另一个函数作用域中的变量的函数）
>
> 换句话说，JS 中任意一个函数都能成为闭包函数

### 创建闭包

通常就是在一个函数内部创建另一个（匿名）函数

```javascript
function say(){
  var name = "zhar";
  return function(){
    return name+"-30";
  }
}
//获取闭包
var hi = say();//Function
console.log(hi());//zhar-30
```

上面示例中，第三行代码做为 say 方法的一个内部函数，访问了外部的 name 属性，该函数被返回，在其它地方被调用时，仍然可以访问到 say 方法内的 name 属性

>根据前面所学的作用域链的知识，可以得出匿名函数中 包含了外部作用域中的 name 属性的引用，当匿名函数被返回后，它的作用域链包含上级函数（say）的活动对象（name）。这样，当 say 函数执行完成后，其活动对象也并不会被销毁，因为匿名函数的作用域链仍然在引用这个活动对象。

*由于闭包所引用的变量不会被自动销毁的特性，在使用闭包时要非常小心，有可能会引起内存占用过多*

### 闭包常见场景

1. 循环添加事件 BUG

   ```javascript
   var lis = document.querySelectorAll("li");
   for(var i=0;i<lis.length;i++){
     lis[i].onclick = function(){
       console.log(this.innerHTML,i);
     }
   }
   //在上面的代码中，innerHTML能够得到正确的值，但 i 并不能，这是由于在 onclick 所指定的匿名函数中使用了外部环境中的同一个活动对象 "i"，当外部环境执行完成后，i 的值是 lis 的长度，所以引用了所有 i 的对象值都变为了 lis的长度
   //修改代码如下：
   for (var i = 0; i < lis.length; i++) {
       lis[i].onclick = (function(index) {
           return function() {
               console.log(lis[index].innerHTML, index);
           }
       })(i);
   }
   //修改后的代码将 onclick 指定的匿名函数改为了立即执行，并将外部环境的 i 值做为参数传递给该函数(参数是做为值传递)，而在匿名函数内部，由一个闭包函数来引用 index

   //另一种方法(这种方法只是做为一种扩展，与本知识点无关)
   for (var i = 0; i < lis.length; i++) {
       lis[i].index = i;
       lis[i].onclick = function() {
           console.log(this.innerHTML, this.index);
       };
   }
   ```

2. 使用闭包封装一个完整的对象操纵功能

   对于一些封装，开发者并不想对外暴露一些属性或变量，此时，可通过闭包来创建

   ```javascript
   var PERSON = function() {
       var obj = {
           name: "zhar",
           age: 30,
           address: "北京"
       };
       return {
           get: function(pName) {
               return obj[pName];
           },
           add: function(pName, pVal) {
               obj[pName] = pVal;
           },
           del: function(pName) {
               delete obj[pName];
           },
           update: function(pName, pVal) {
               obj[pName] = pVal;
           }
       }
   }();
   console.log(PERSON.get("name"));//zhar
   PERSON.add("desc","175");
   console.log(PERSON.get("desc"));//175
   PERSON.del("age");
   console.log(PERSON.get("age"));//undefined
   ```



## 关于作用域、闭包的一些经典题目

```javascript
function f1() {
    var n = 999;
    nAdd = function() {
        n += 1
    }
    function f2() {
        console.log(n);
    }
    return f2;
}
var result = f1();
result();
nAdd();
result();
```

```javascript
function show(i) {
    var a = i;
    function inner() {
        a = 10;
        console.log(1,a);
    }
    inner();
    console.log(2,a);
}
var a = 0;
show(5);
console.log(3,a);
```

```javascript
var too = "old";
if (true) {
    var too = "new";
}
console.log(1,too);
function test() {
    var too = "new";
}
test()
console.log(2,too);
```

```javascript
var i = 0;
function outPut(i) {
    console.log(i)
}
function outer() {
    outPut(i);
    function inner() {
        outPut(i);
        var i = 1;
        outPut(i);
    }
    inner();
    outPut(i);
}
outer();
```

```javascript
function fun(n, o) {
    console.log(o)
    return {
        fun: function(m) {
            return fun(m, n);
        }
    };
}
var a = fun(0);
a.fun(1);
a.fun(2);
a.fun(3);
var b = fun(0).fun(1).fun(2).fun(3);
var c = fun(0).fun(1);
c.fun(2);
c.fun(3);
```

```javascript
var a = function(){
    a=2;
    console.log(1111,a);
}
function a(){
    a=1;
    console.log(2222,a);
}
a();
a();
```



## this

this 是 JS 中的一个关键字，代表了函数运行时，自动生成的一个内部对象，只能在函数内部使用

我们要讨论的是 this 的指向

this 的指向并不能在创建是就决定了，而是由执行环境决定的

### this 指向

#### 全局环境（纯粹的函数调用）

在全局环境下，this 就代表 window（这是针对 WEB 应用来讲的）

```javascript
var name = 'zhar';
function say(){
  console.log(this.name);//zhar
}
say();
```

同样，在 setTimeout 或 setInterval 这样的延时函数中调用也属于全局对象

```javascript
var name = 'zhar';
setTimeout(function(){
  console.log(this.name);//zhar
},0);
```



#### 对象环境

```javascript
var obj = {
  name : "zhar",
  say : function(){
    console.log(this.name);//zhar
  }
}
obj.say();
```

如上面的代码所示，函数被其所必对象调用，那么 this 便指向 obj 对象

观察下面的代码

```javascript
var name = 'tom';
var obj = {
  name : "zhar",
  say : function(){
    console.log(this.name);
  }
}
var fun = obj.say;
fun();//输出 ?
```



另外一种情况：

```javascript
var name = 'tom';
var obj = {
  name : "zhar",
  say : function(){
    return function(){
      console.log(this.name);
    }
  }
}
obj.say()();//输出 ？
```



#### 构造函数环境

构造函数中 this 会指向创建出来的一个对象，使用new调用构造函数时，会先创建出一个空对象，然后调用call函数把构造函数中的指针修改为指向这个空对象，执行完构造函数后，这个空对象也就有了相关的属性方法并返回这个对象。

```javascript
function Person() {
    this.name = 'zhar';
}
var p = new Person();
console.log(p.name);
```

*构造函数不需要返回值，如果指定返回值要小心，指定返回一个对象，则 this 的指向将发生变化*

```javascript
function Person() {
  this.name = 'zhar';
  return {};
}
var p = new Person();
console.log(p.name);//undefined
//--------------------------------------
function Person() {
  this.name = 'zhar';
  return {name:'tom'};
}
var p = new Person();
console.log(p.name);//tom      如果构造函数返回对象(Object,Array,Function)，那 this 将指向这个对象，其它基础类型则不受影响
//--------------------------------------
function Person() {
  this.name = 'zhar';
  return 1;//number string boolean 等
}
var p = new Person();
console.log(p.name);//zhar
```



#### 事件环境

在 DOM 事件中使用 this，this 指向了触发事件的 DOM 元素本身

```javascript
li.onclick = function(){
	console.log(this.innerHTML);
}
```



### 更改 this 指向

1. 使用局部变量替代

   ```javascript
   var name = "zhar";
   var obj = {
     name : "zhar",
     say : function(){
       var _this = this;//使用一个变量指向 this
       setTimeout(function(){
         console.log(_this.name);
       },0);
     }
   }
   obj.say();
   ```

   ​

2. 使用 call 或 apply 方法

   call 是函数的一个方法，MDN上的官方定义为：call方法调用一个函数, 其具有一个指定的`this`值和分别提供的参数

   语法：

   `fun.call(thisObj[,arg1[,arg2[,...]]])`

   通俗来讲：call 用来更改 this 的指向

   通过一系列代码来展示 call 的用法：

   ```javascript
   var name = 'zhar';
   function say(){
     console.log(this.name);
   };
   say();//zhar;
   var obj = {
     name : 'tom',
     say : function(){
       console.log(this.name);
     }
   }
   say.call(obj);//tom      将 say 函数中的 this 替换为传入的对象
   obj.say();//tom
   obj.say.call(null);//zhar    将 obj.say 函数的 this 替换为了 null，也就意味着指向了全局环境
   ```

   ```javascript
   //前面课程的继承代码
   function Person(){
     this.name = "人";
   }
   function Student(){
     Person.call(this,null);
   }
   var s = new Student();
   console.log(s.name);
   ```

   ```javascript
   li.onclick = function(){
     console.log(this.innerHTML);//此处的 this 代表着 DOM 元素
     function update(){
       this.innerHTML += " new ";
     }
     //update();//这样做的话，this 的指向将变为window
     update.call(this);//通过 call 方法修改函数内 this 的指向
   }
   ```

   ```javascript
   //call 的传参
   function say(arg1,arg2){
     console.log(this.name,arg1,arg2);
   };
   var obj = {
     name : 'tom',
     say : function(){
       console.log(this.name);
     }
   }
   say.call(obj,'one','two');//tom one two
   ```

   ​

   **apply**

   applay 与 call 的作用相同，不同之处在于传参的形式，apply 是以数组的形式传递参数的，而 call 方法的参数可以是任意类型

   ```javascript
   //apply 的传参
   function say(arg1,arg2){
     console.log(this.name,arg1,arg2);
   };
   var obj = {
     name : 'tom',
     say : function(){
       console.log(this.name);
     }
   }
   say.apply(obj,['one','two']);//tom one two
   ```



## 堆栈

理解了堆栈内存才会对值引用和地址引用有更好的理解

栈（stack）和堆（heap）

stack 为自动分配的空间，它由系统自动释放

heap 为动态分配的内存，大小不定也不会自动释放

基本数据类型存放于栈内存中，Undefined Null String Number Boolean，它们是直接按值存放的，可以直接访问

引用数据类型存放于堆内存中，变量只是保存的一个指针，该指针指向堆内存中的地址，当访问引用类型数据（Array、Object、Function等）时，先从栈中获得该对象的指针，再从堆中取出对象的数据



**值传递与地址传递**

```javascript
var a = 10;
var b = a;
b = 20;
console.log(a,b);
//以上的代码修改 b 的值并不会影响 a 的值

var a = [1,2,3,4];
var b = a;
var c = a[0];
console.log(b);
console.log(c);
b[0] = 9;
c = 10;
console.log(a);//[9,2,3,4]
//以上代码可以看出 当改变 b 中的数据时，a 中的数据也发生了变化；改变 c 的 数据时，a 不会受影响
//a 是数组，属引用类型，当将 a 赋值给 a 时，传递的是栈中的地址，而不是堆内存中的对象。
//而 c 只是从 a 堆内存中获取的一个数据值，保存于栈中。修改 c 时，是在栈中直接修改
```



**浅拷贝与深拷贝**

在定义引用数据类型时，变量存放的只是一个地址。当使用对象拷贝，传递的也只是一个地址。因此在访问拷贝对象属性时，会根据地址找到源对象指向的堆内存中。

​	![](http://opm7nn1ul.bkt.clouddn.com/20170611149718201316196.jpg)



*浅拷贝*

```javascript
var obj1 = {
    name : "zhar",
    desc : ["北京"]
}
function copy(o1){
    var newObj = {};
    for(var key in o1){
        newObj[key] = o1[key];
    }
    return newObj;
}
// var obj2 = obj1;
var obj2 = copy(obj1);
obj2.name = "tom";
obj2.desc.push("昌平");
console.log(obj1);//{ name: 'zhar', desc: [ '北京', '昌平' ] }
```

![](http://opm7nn1ul.bkt.clouddn.com/20170611149718289591532.jpg)



*深拷贝*

```javascript
var obj1 = {
    name : "zhar",
    desc : ["北京"]
}
function copy(obj,target){
    var newObj = target || {};
    console.log(obj)
    for(var key in obj){
        if(typeof obj[key] === "object"){
            newObj[key] = (obj[key].constructor===Array)?[]:{};
            copy(obj[key],newObj[key]);
        }else{
            newObj[key] = obj[key];
        }
    }
    return newObj;
}
var obj2 = copy(obj1);
```



## 垃圾回收

Javascript 具有自动垃圾回收机制，执行环境会管理代码执行过程中使用的内存。

使我们不必像 C 或 C++开发者那样，手动去管理内存的释放。

自动回收机制原理：

垃圾回收器按照固定的时间间隔找出不再继续使用的变量，然后释放其占用的内存。

两种策略：

1. 标记清除

   最常用的垃圾回收方式。当变量进入环境时，将变量标记为"进入环境"；当变量离开环境时，将其标记为"离开环境"。

   到2008年，各浏览器使用的清除策略都是标记清除，差别在于时间间隔不同

2. 引用计数

   引用计数是跟踪每个值被引用的次数；当有一个变量被引用时，则这个值的引用次数加1，当取消一个引用时，次数减1。当引用值变为0 时，将由垃圾收集器回收

   引用计数方式有严重的问题，就是，当变量相互引用时，如：

   ```javascript
   var obj1 = {}
   var obj2 = {}
   obj1.a = obj2;
   obj2.b = obj1;
   ```

   对于上面的代码，存在相互引用，其引用计数永远为2，就会导致对象永远不会被回收。

   > obj = null;