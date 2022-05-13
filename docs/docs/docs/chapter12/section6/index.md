# 原型链与继承理解

https://www.jianshu.com/p/dee9f8b14771
https://www.jianshu.com/p/652991a67186
https://www.jianshu.com/p/a4e1e7b6f4f8
https://www.cnblogs.com/Yirannnnnn/p/4896542.html
https://www.cnblogs.com/thonrt/p/5900510.html
http://www.cnblogs.com/onepixel/p/5143863.html
https://blog.csdn.net/qq_29820901/article/details/89636619(继承)

普通对象/函数对象/原型对象（三者本质上是json格式的普通对象）

一定要把原型对象想象成是一个独立的对象，或者用一个方框来代表它

```
var o1 = {}; 
var o2 =new Object();
var o3 = new f1();

function f1(){}; 
var f2 = function(){};
var f3 = new Function('str','console.log(str)');

console.log(typeof Object); //function 
console.log(typeof Function); //function  

console.log(typeof f1); //function 
console.log(typeof f2); //function 
console.log(typeof f3); //function   

console.log(typeof o1); //object 
console.log(typeof o2); //object 
console.log(typeof o3); //object
```

凡是通过 new Function() 创建的对象都是函数对象，其他的都是普通对象。Function Object 也都是通过 New Function()创建的。
f1,f2,归根结底都是通过 new Function()的方式进行创建的。

例：
```
function Person(){}

var person = new Person();
```

函数对象，当在new创造实例时，函数对象又可称为构造函数，指的是同一个东西

实例（new出来的）的属性constructor指向构造函数本身(按规定一般首字母大写）

①即：实例对象.constructor === 函数对象 => person.constructor === Person

每个对象都有 __proto__ 属性，但只有函数对象才有 prototype 属性

函数对象都有一个prototype 属性，这个属性指向构造函数的原型对象。

②即：函数对象.prototype === 构造函数.原型对象

JS 在创建对象（不论是普通对象还是函数对象）的时候，都有一个叫做__proto__ 的内置属性，用于指向创建它的构造函数的原型对象。

③即：实例对象.__proto__ === 构造函数.原型对象

由②③推出
实例对象.__proto__ === 实例对象的构造函数.prototype 
=> person.__proto__ === Person.prototype

原型对象都会自动获得一个 constructor（构造函数）属性，这个属性（是一个指针）指向 prototype 属性所在的函数

④即：原型对象.constructor === 函数对象 ==> Person.prototype.constructor === Person


所以，原型链
```
person.constructor == Person;
person.__proto__ == Person.prototype;
Person.prototype.constructor == Person;
```
注意：constructor属性是定义在原型对象上面，意味着也可以被实例对象继承


Person.prototype.constructor一般是指向Person，但若是直接改变Person.prototype的指向，比如Person.prototype = {}，那么此时Person.prototype.constructor指向的便是Object;即Person.prototype.constructor===Object，而person.constructor也===Object

问题：

1. person1.__proto__ 是什么？
2. Person.__proto__ 是什么？
3. Person.prototype.__proto__ 是什么？
4. Object.__proto__ 是什么？
5. Object.prototype__proto__ 是什么？

答案：

第一题：

因为 person1.__proto__ === person1 的构造函数.prototype

因为 person1的构造函数 === Person

所以 person1.__proto__ === Person.prototype


第二题：

因为 Person.__proto__ === Person的构造函数.prototype

因为 Person的构造函数 === Function

所以 Person.__proto__ === Function.prototype


第三题：

Person.prototype 是一个普通对象，我们无需关注它有哪些属性，只要记住它是一个普通对象。
因为一个普通对象的构造函数 === Object

所以 Person.prototype.__proto__ === Object.prototype


第四题，参照第二题，

因为 Person 和 Object 一样都是构造函数


第五题：

Object.prototype 对象也有proto属性，但它比较特殊，为 null 。
因为 null 处于原型链的顶端，这个只能记住。

Object.prototype.__proto__ === nul

