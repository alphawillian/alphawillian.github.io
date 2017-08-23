## 原型

简单创建一个构造函数与实例：

```javascript
function Person() {

}
Person.prototype.name = 'Zhar';
Person.prototype.age = 30;
Person.prototype.say = function(){
  console.log(this.name);
}
var person1 = new Person();
var person2 = new Person();
console.log(person1.name) // Zhar
console.log(person2.name) // Zhar
```

> Person 构造函数
>
> person 是 Person 的一个实例对象

### instanceof

instanceof 用于判断一个对象是否是另一个对象的实例（该方法在原型链中依然有效）

```javascript
console.log(person1 instanceof Person);//true
console.log(person2 instanceof Person);//true
var arr = new Array();
console.log(arr instanceof Array);//true
```



### prototype

**每个函数都有一个 prototype属性(只有函数)**

*prototype 指向？*

函数的 prototype属性指向了一个对象，这个对象就是调用该构造函数而创建的实例的原型，也就是上面代码中的 person1 person2的原型对象。使用原型对象的好处就是可以让把有对象的实例共享它所包含的属性和方法。换句话说，不必在构造函数中定义对象实例的信息，而是可以将这些信息直接添加到原型对象中去。



### constructor

默认情况下，所有原型对象都会自动获得一个 *constructor（构造器）*属性，这个属性指向所在的函数。

`Person.prototype.constructor` 指向 `Person`

![](http://blog.gethin.tech/img/prototype1.jpg)



任何情况下，只要创建了一个新函数，就会为该函数创建一个 *prototype* 属性，这个属性指向函数的原型对象。

*"原型"是什么？*

**每一个对象都有一个与之关联的另一个'原型'对象，每一个对象都会从原型继承属性**

*如何访问对象的原型？*

在 ECMA中，并没有规定直接访问对象原型的方法，但 Firefox/Chrome/safari 在每一个对象都支持一个`__proto__`用来访问对象的原型。

### \_\_proto\_\_

```javascript
console.log(Person.prototype);
console.log(person);
console.log(person.__proto__);
```

`__proto__`存在于实例与构造函数的原型对象之间，而不是存在于实例与构造函数之间

![](http://blog.gethin.tech/img/prototype2.jpg)



### isPrototypeOf

可通过`object1.isPrototypeOf(object2)`来判断一个对象是否存在于另一个对象的原型链中，即 object1 是否存在于 object2的原型链中。换句话讲：判断 object2是否有一个指针指向 object1

```javascript
Person.prototype.isPrototypeOf(person1);//true
Person.prototype.isPrototypeOf(person2);//true
```



### getPrototypeOf（ES5）

返回一个对象的原型

```javascript
Object.getPrototypeOf(person1);
Object.getPrototypeOf(person2);
```



*一些判断*

```javascript
person.__proto__ === Person.prototype;//true
person.__proto__.constructor === Person.prototype.constructor;//true
person1.__proto__ === person2.__proto__;//true
person1.say === person2.say;//true
Object.getPrototypeOf(person1) === Object.getPrototypeOf(person2);//true
Object.getPrototypeOf(person1) === Person.prototype;//true
```



### hasOwnProperty

观察下面的代码：

```javascript
console.log(pserson1.name);//Zhar   来自于原型
console.log(person1.address);//undefined
person1.name = "new Name";
console.log(person1.name);//new Name   来自于实例
console.log(person2.name);//Zhar   来自于原型
```



*寻找规则？*

当代码读取某个对象的某个属性时，首先会从实例本身开始，如果在实例中找到了该属性，则返回该属性的值；如果没有找到，则搜索指针指向的原型对象，如果找到，则返回值，如果没有则继续向上寻找，直到找到 Obejct

虽然可以通过对象实例访问原型中的值，但不能通过对象实例重写原型中的值。

如果在实例中添加了一个属性，而该属性与实例原型中的一个属性名相同，则会在实例中创建该属性，该属性将会屏蔽原型中的那个同名属性（可以通过 delete 删除实例属性，恢复原型属性的访问）

```javascript
delete person1.name;
console.log(person1.name);//Zhar 来自于原型
```

> 通过`hasOwnProperty(propertyName)`来检测一个属性是存在于实例还是原型中

```javascript
console.log(person1.hasOwnProperty("name"));//false;
person1.name = "newName";
console.log(person1.hasOwnProperty("name"));//true
```

![](http://blog.gethin.tech/img/prototype3.jpg)

### in

观察以下代码：

```javascript
console.log("name" in person1);//true;
person1.name = "newName";
console.log("name" in person1);//true
```

通过`in`关键字可以判断某个对象是否包含某个属性，而不管这个属性是属于实例还是原型

> 通过 hasOwnProperty() 与 in 相结合，可精确定位一个属性是属于原型还是实例



### keys(es5)

`keys(object)`方法可返回对象所有可枚举属性的字符串数组

```javascript
console.log(Object.keys(person1));//[]
person1.name = "newName";
console.log(Object.keys(person1));//["name"]
console.log(Object.keys(Person.prototype));//[ 'name', 'age', 'say' ]
```

*keys()返回的是该对象的属性数组，并不包含其构造函数上的属性*



*通过字面量对象创建原型的问题？*



## 继承

### 原型链式继承

让一个函数的原型对象指向另一个原型的指针，而另一个原型中也包含指向另外一个构造函数的指针，依此层层嵌套，形成原型链

```javascript
function Animal() {
    this.topType = "脊椎动物";
}
Animal.prototype.getTopType = function(){
    return this.topType;
}
function Person() {
    this.secondType = "人类";
}

Person.prototype = new Animal();
Person.prototype.getSecondType = function(){
    return this.secondType;
}
var person = new Person();
console.log(person.topType+"--"+person.secondType);//脊椎动物--人类
```

在上面的例子中，`Person.prototype=new Animal()` 即是将 Animal 实例赋给了 Person.prototype

结合前面原型中的讲解，new Animal 产生一个 Animal 实例，该实例对象有一个`__proto__`指向其构造函数的`prototype`，将该实例赋值给 Person 的原型，意味着Person的原型 既拥有了 Animal 的所有实例属性或方法，还包含了一个指向 Animal的原型的指针。

> 思考：person.constructor 等于什么？

示意图如下：

![](http://blog.gethin.tech/img/prototype4.jpg)



我们可以再扩展代码：

```javascript
//省略....
function Student(){
    this.thirdType = "学生";
}
Student.prototype = new Person();
var student = new Student();
console.log(student.topType+"--"+student.secondType+"--"+student.thirdType);//'脊椎动物--人类--学生'
```

在这里增加了第三个构造函数`Student`，并且将 `Person` 实例赋给了 `Student.prototype`，这样，层级关系将变为 Student 继承了 Person，而 Person 继承了 Animal；Student 拥有 Person 和 Animal 的全部共享属性或方法，Person 拥有 Animal 的全部共享属性或方法

扩展后的示意图：

![](http://blog.gethin.tech/img/prototype5.jpg)



**搜索过程？**

正如上面的例子，在调用 student.topType 时，是如何得到"脊椎动物"的，在前面的`hasOwnProperty`知识中，曾经有过涉及，即：

1. 搜索实例本身
2. 搜索 Student.prototype
3. 搜索 Person.prototype
4. 搜索 Animal.prototype  （查找到属性，返回属性值）

如下图：

![](http://blog.gethin.tech/img/prototype6.jpg)



**Object？**

在上面的原型链图中，可以看到在寻找某属性时，一直找到了 Object.prototype。所有函数的默认原型都是 Object 的实例，因此默认原型都会包含一个指针指向 Object.prototype。而这也是对象或函数能够使用`toString()`方法的原因

更完善的原型链示意图：

![](http://blog.gethin.tech/img/prototype7.jpg)



> 原型链式继承所带来的问题？

1. 引用数据类型问题

   在**JS面向对象**一节中，我们曾经尝试将引用数据类型赋值给原型的属性（参见<u>创建对象</u>一节的<u>原型模式</u>），结果出现实例间相互影响，在原型继承时，也会出现此问题，观察下例：

   ```javascript
   function Person(){
     this.desc = ["北京"];
   }
   function Student(){}
   Student.prototype = new Person();
   var s1 = new Student();
   var s2 = new Student();
   s1.desc.push("昌平");
   console.log(s1.desc);//[ '北京', '昌平' ]
   console.log(s2.desc);//[ '北京', '昌平' ]
   ```

   Person 函数含有一个desc 属性，则 Person的实例会包含各自的一个 desc 属性。但此时的 Student 通过原型链继承了 Person，Student 的 prototype 则变成了 Person 的一个实例，则 Student 就拥有了一个 desc 属性，相当于使用 Student.prototype.desc=[…]为 Student 添加了这样一个原型属性，结果显而易见。

2. 传参问题

   通过前面的代码，会发现，在使用原型链继承时，无法向父级构造函数传递参数



### 借用构造函数式继承

通过使用 call 或 apply 来实现借用式继承

call 或 apply ：更改函数执行的上下文环境（call 和 apply 的用法在后面的课程中会有讲解）

```javascript
function Person(name){
  this.name = name;
}
function Student(name){
  Person.call(this,name);
}
var student = new Student("zhar");
console.log(student.name);//zhar
```

上面的代码中，使用了 `call`，在 Student 的实例环境中调用了 Person 构造函数，更改了 Person 的 this 指向为当前实例环境，最终 Student的实例就拥有了 name 属性

**优势**

可以在调用构造函数时，向'父级'传递参数，如上例中一样，在调用 Person 时传入了 name 值

**缺点**

无法继承原型属性或方法

```javascript
function Person(name){
  this.name = name;
}
Person.prototype.say = function(){
  console.log(this.name);
}
function Student(name){
  Person.call(this,name);
}
var student = new Student("zhar");
console.log(student.name);//zhar
student.say();//报错  student.say is not a function
```



### 组合继承

将原型链式继承与借用构造函数式继承组合在一起，结合二者的优势。通过原型链实现对原型属性或方法的继承，通过借用构造函数实现对实例属性的继承

```javascript
function Person(name){
  this.name = name;
}
Person.prototype.say = function(){
  console.log(this.name);
}
function Student(name){
  Person.call(this,name);
}
Student.prototype = new Person();
var student = new Student("zhar");
console.log(student.name);//zhar
student.say();//zhar
```





