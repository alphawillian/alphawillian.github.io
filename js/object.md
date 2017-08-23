## JS 面向对象

在 ECMA 中，将对象定义为*无序属性的集合，这些属性可以包含基本值、对象、函数*。换句话讲，对象无非就是一堆键值对的集合，键用来定义名称，值用来表述数据

### 创建对象（基本）

```javascript
var obj = {};
//或
var obj = new Object();

obj.name = "zhar";
obj.age = 30;
obj.say = function(){
  console.log(this.name);
}
//或(字面量形式)
var obj = {
  name  : "zhar",
  age : 30,
  say : function(){
    console.log(this.name);
  }
}
```

### 属性

1. 访问属性

   - 通过 `.`语法或 `[]`来访问属性
   - 通过`for...in`来遍历属性

   ```javascript
   console.log(obj.name);//zhar
   console.log(obj["name"]);//zhar
   var key = "age";
   console.log(obj[key]);//30
   for(var k in obj){
     console.log(k , obj[k]);
   }
   ```

   ​

2. 定义属性

   - 通过`.`语法或`[]`来定义属性
   - 通过`defineProperty()（ES5）`来定义属性
   - 通过 `defineProperties()（ES5）`来定义多个属性
   - 通过 `getter`和`setter`函数来定义访问器属性

   ```javascript
   //定义属性
   obj.address = "北京";
   obj["desc"] = "程序员";
   ```

   ​

   **defineProperty()定义数据属性**

   一个数据属性包含4个描述行为，分别是：

   *configurable*：是否能够通过 delete 删除属性

   *enumerable*：是否能够通过 for-in 遍历属性

   *writable*：是否能够修改属性的值

   *value*：属性的值，读取时从这个位置读、修改时从这个位置写

   前面的例子中，直接在对象上定义的属性，configurable、enumerable、writable 均为 true，即：可删除、可遍历、可修改，可以通过 defineProperty 方法来定义这几个属性：

   ```javascript
   var person = {};
   Object.defineProperty(person,"name",{
     writable : false,//不可修改
     configurable : false,//不可删除
     enumerable : false,//不可遍历
     value : "zhar"
   });
   console.log(person.name);//zhar
   person.name = "newName";
   console.log(person.name);//zhar
   delete person.name;
   ```

   *如果手动调用 defineProperty，那上述的三个属性都会默认为 false*

   ​

   **defineProperties**

   使用*defineProperties*定义多个属性

   ```javascript
   var person = {};
   Object.defineProperties(person,{
     name : {
       value : "zhar"
     },
     age : {
       configurable : true,
       value : 30
     },
     //...
   })
   ```

   其使用方法与单个`defineProperty`类似，接受两个对象参数，第一个对象为要添加属性的对象，第二个对象为添加的属性。

   ​

   **getter 与 setter**

   访问器属性，包含一对 getter 与 setter 函数，分别用来读取及写入值，包含以下四个特性：

   *configurable*：是否能够通过 delete 删除属性

   *enumerable*：是否能够通过 for-in 遍历属性

   *get*：读取属性时调用的函数，默认 Undefined

   *set*：写入属性时调用的函数，默认 Undefined

   ```javascript
   var person = {
     firstname : "zhar",
     lastname : "zi"
   };
   Object.defineProperty(person,"name",{
     get : function(){
       return this.firstname + "-"+this.lastname
     },
     set : function(nName){
       if(nName.indexOf("best")!=-1){
         this.firstname = nName;
       }
     }
   })
   ```

   *getter 与 setter 并不是必须指定的，只指定 getter意味着属性不可写，只指定 setter 意味着属性不可读*

   **getOwnPropertyDescriptor**

   获取属性描述符

   ```javascript
   var person = {
     name : "zhar"
   }
   console.log(Object.getOwnPropertyDescriptor(person,"name"));
   ```

   ​

3. 删除属性

   - `delete`



### 创建对象（进阶）

#### 字面量模式

正如前面所展示的示例一样，即为字面量模式构建对象

```javascript
//字面量模式创建对象
var person1 = {
  name : "zhar",
  age : 30
}
//var person2 = ...
//var personN = ...
```

> 缺点：将出现大量重复冗余的代码

#### 工厂模式

为了解决字面量模式创建对象的重复代码问题，可以将创建过程抽象出来成为一个工厂，即：使用一个函数来创建对象

```javascript
function createPerson(name,age){
  var obj = {};
  obj.name = name;
  obj.age = age;
  return obj;
}
var person1 = createPerson("zhar",30);
//var person2 = createP...
//var personN = createP...
```

> 缺点：虽然工厂模式可以解决字面量创建对象时的重复代码，但并没有解决对象类型识别的问题，即：怎样知道这个对象属于什么类（如：Object Array 这种类型定义）

#### 构造函数模式

对比以下代码：

```javascript
function Person(name,age){
  this.name = name;
  this.age = age;
  this.say = function(){
    console.log(this.name+"-"+this.age);
  }
}
var person1 = new Person("zhar",30);
person1.say();//zhar-30
var person2 = new Person("tom",19);
person2.say();//tom-19
```

上面便是一个典型的构造函数，通过观察可以发现，有一些不同的地方：

- Person 函数并没有返回值，而是将 name、age 等属性赋给了 this 关键字
- Person 函数的首字母`P`是大写
- 在调用该对象时，使用的是 `new Person`
- Person 函数没有`return`关键字

构造函数实际也是一个普通的函数，首字母大写是惯例，用来表明该函数是一个构造函数，用于区分。

person1、person2被称为 Person 的*实例*

*如果不使用 `new`去调用构造函数会怎样？*

```javascript
function Person(name,age){
  this.name = name;
  this.age = age;
  this.say = function(){
    console.log(this.name+"-"+this.age);
  }
}
Person("zhar",30);
//say函数哪里去了？
window.say();
```

> 缺点：每个方法（上例中的 say 方法）会在每一个实例上创建一次（上例中的 person1、person2实例），这样做是没有必要的
>
> 可通过 person1.say === person2.say 去判断



#### 原型模式

在 Javascript 中，每一个函数都有一个原型(prototype)属性，该属性指向一个对象，该对象可被所有实例共享，也就意味着，我们可以不必在构造函数中指定属性，而将这些属性或方法都添加到原型中去

```javascript
function Person(){}
Person.prototype.name = "zhar";
Person.prototype.age = 30;
Person.prototype.say = function(){
  console.log(this.name + "-" +this.age);
}
var person1 = new Person();
person1.say();//zhar-30
var person2 = new Person();
person2.age = 50;
person2.say();//zhar-50
```

> 缺点：原型模式固然可以减少函数的重复创建，但问题在于所有实例都共享了同样的属性（上例中的 name、age），这在函数或简单类型的值时没有太大的问题，但在引用数据类型中，则会带来重大的问题。
>
> ```javascript
> //省略...
> Person.prototype.desc = ["北京","海淀"];
> //省略...
> person2.desc[1]="昌平";
> console.log(person1.desc,person1.age);//[ '北京', '昌平' ] 30
> console.log(person2.desc,person2.age);//[ '北京', '昌平' ] 55
> ```
>
> 正如上例所示，引用数据类型是相互影响的

#### 构造函数与原型模式

结合了上面所有模式的缺点，我们可以使用混合构造函数与原型的模式去构建对象

```javascript
function  Person(name,age){
  this.name = name;
  this.age = age;
  this.address = ["北京"];
}
Person.prototype.say = function(){
  console.log(this.name+"-"+this.age+"-"+this.address);
}
var person1 = new Person("zhar",30);
person1.address.push("昌平");
var person2 = new Person("tom",15);
person2.address.push("海淀");
person1.say();//zhar-30-北京,昌平
person2.say();//tom-15-北京,海淀
```

> 这种模式既支持像构造函数传参，又可以共享对方法的引用，节省内存
>
> 这是最为推荐的一种形式