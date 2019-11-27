[TOC]

#### `prototype`

每个函数都有一个`prototype`属性

那个这个`prototype`到底指向什么呢? 其实, `prototype`指向了一个对象, 这个对象正是调用构造函数而创建的实例的原型. 在下面的例子中, 就是 person1 和 person2 的原型.

那么原型有什么作用呢? 其实可以把它理解为所有实例的共享对象, 写在这个原型中的属性, 会被所有的实例所共享.

```
function Person() {

}

// 虽然写在注释里，但是你要注意：prototype是函数才会有的属性
Person.prototype.name = 'Kevin';
var person1 = new Person();
var person2 = new Person();
console.log(person1.name) // Kevin
console.log(person2.name) // Kevin
```

#### `__proto__`

每一个 JavaScript 对象(除了 null )都具有的一个属性，叫 `__proto__`，这个属性会指向该对象的原型, 即`prototype`

```
function Person() {

}
var person = new Person();
console.log(person.__proto__ === Person.prototype); // true
```

#### constructor

既然实例对象和构造函数都可以指向原型，那么原型是否有属性指向构造函数或者实例呢？

指向实例的没有, 但是指向构造函数的有, 即`constructor`

```
function Person() {

}
console.log(Person === Person.prototype.constructor); // true
```

#### 构造函数、实例原型、和实例之间的关系
![image](https://github.com/ShuBuShao/blog/blob/master/image/prototype4.png)
