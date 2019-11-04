[TOC]

js 继承一共有 7 中方式:

- 原型链继承
- 构造函数继承
- 组合继承
- 原型式继承
- 寄生式继承
- 寄生组合继承
- ES6 继承

用一张图可以说明几种继承方式之间的关系
![image](https://github.com/ShuBuShao/blog/blob/master/image/extend.jpg)

#### 原型链继承

核心:

- 将子类的 prototype 设置为父类的实例
- 将子类的构造函数指向自身

优点:

- 父类方法可以复用

缺点:

- 父类的引用类型属性会被子类共享
- 子类构建实例时不能向传参

```
function Person(name, age) {
  this.name = name || 'unknow';
  this.age = age || 0;
  this.hobbies = ['music', 'reading'];
}

Person.prototype.say = function() {
  console.log('I am a person');
};

function Student(name) {
  this.name = name;
  this.score = 80;
}

// 注意, 继承必须要写在子类方法定义的前面
Student.prototype = new Person();
Student.prototype.constructor = Student;

// 为子类新增一个方法(在继承之后,否则会被覆盖)
Student.prototype.study = function() {
  console.log('I am studing');
};

// ------使用------
let stu = new Student('lucy');
console.log(stu.name); // lucy 子类自己的属性
console.log(stu.age); // 0 继承父类的属性 子类构建实例时不能向父类传参
console.log(stu.score); // 80 子类自己的属性

// 缺点展示
stu1.hobbies.push('basketball'); // 所有实例都会受到影响
console.log(stu1.hobbies); // music,reading,basketball
console.log(stu2.hobbies); // music,reading,basketball
```

#### 构造函数继承(经典继承)

核心:

- 在子类里面执行父类的构造函数(相当于把父类的代码复制到子类里面执行了一遍)

优点:

- 解决类引用类型被所有实例共享的问题
- 子类构建实例时可以向父类传参

缺点:

- 函数也是引用类型, 每实例化一个子类, 都相当与吧函数也复制了一遍

```
function Person(name, age) {
  this.name = name || 'unknow';
  this.age = age || 0;
  this.hobbies = ['music', 'reading'];
  this.run = function() {
    console.log('I am running');
  };
}

Person.prototype.say = function() {
  console.log('I am a person');
};

function Student(name, age) {
  Person.call(this, name, age);
  this.score = 80;
}

// 为子类新增一个方法(在继承之后,否则会被覆盖)
Student.prototype.study = function() {
  console.log('I am studing');
};

// ------使用------
let stu = new Student('lucy');
console.log(stu.name); // lucy 子类自己的属性
console.log(stu.age); // 0 继承父类的属性 子类构建实例时不能向父类传参
console.log(stu.score); // 80 子类自己的属性
stu.study();
// stu.say(); //报错 无法使用父类原型中的方法
stu.run();

let stu2 = new Student('tom');
console.log(stu.run === stu2.run); //false 每个实例都复制一遍相同的方法 造成浪费
```

#### 组合继承(伪经典继承)(类式继承)

核心:

- 组合继承就是各取上面两种继承的长处

优点:

- 父类方法可以复用
- 父类的引用属性不会被子类实例共享
- 子类构建实例时可以想父类传参

缺点:

- 调用两次父类的构造函数, 造成性能上的浪费

```
function Person(name, age) {
  this.name = name || 'unknow';
  this.age = age || 0;
  this.hobbies = ['music', 'reading'];
}

Person.prototype.say = function() {
  console.log('I am a person');
};

function Student(name, age) {
  Person.call(this, name, age); // 使用构造函数继承
  this.score = 80;
}

Student.prototype = new Person(); // 使用原型链继承
Student.prototype.constructor = Student;

// 为子类新增一个方法(在继承之后,否则会被覆盖)
Student.prototype.study = function() {
  console.log('I am studing');
};

// ------使用------
let stu = new Student('lucy');
console.log(stu.name); // lucy 子类自己的属性
console.log(stu.age); // 0 继承父类的属性 子类构建实例时不能向父类传参
console.log(stu.score); // 80 子类自己的属性
stu.study();
stu.say();

let stu2 = new Student('tom');
console.log(stu.say === stu2.say); //ture 每个实例都复制一遍相同的方法 造成浪费
```

#### 原型式继承

```
function create(o) {
  let F = function() {};
  F.prototype = o;
  return new F();
}
```

核心:

- 对参数对象就行浅复制

优点:

- 父类方法可以复用

缺点:

- 父类引用属性会被实例共享
- 子类构建实例时不能向父类传参

原型式继承也是借助 prototype 继承, 所以会与原型链继承有一样的优缺点, 与原型链继承不同的是, 原型式继承接收的参数不一定是构造函数, 可以是任何对象

`Object.create`函数, 也是基于原型时继承的

```
let Person = {
  name: 'unknow',
  age: 1,
  hobbies: ['music', 'reading'],
};

let stu1 = create(Person);
stu1.name = 'lucy';
stu1.score = 80;
stu1.hobbies.push('math');

let stu2 = create(Person);
stu2.name = 'tom';
stu2.hobbies.push('art');

// ------使用------
console.log(stu1.name); // lucy
console.log(stu1.age); // 1
console.log(stu1.score); // 80 子类自己的属性

console.log(stu1.hobbies); // music,reading,math,art 共享引用属性
console.log(stu2.hobbies); // music,reading,math,art 共享引用属性
```

#### 寄生式继承

核心:

- 原型式继承的继承上进行增强

优点:

- 提供一种思路, 没什么优点

缺点:

- 函数无法复用

```
function cloneAndStrengthen(proto) {
  function F() {}
  F.prototype = proto;
  let f = new F();
  // 在实例化对象返回之前, 增加功能
  f.say = function() {
    console.log('I am a person');
  };
  return f;
}
```

#### 寄生组合继承

核心:

- 降低组合继承调用构造函数的开销

```
function create(o) {
  let F = function() {};
  F.prototype = o;
  return new F();
}

function extend(subType, superType) {
  let prototype = create(superType.prototype); // 创建了父类原型的浅复制
  prototype.constructor = subType; // 将子类的原型替换为这个原型
  subType.prototype = prototype; // 修正原型的构造函数
}

function Person(name, age) {
  this.name = name || 'unknow';
  this.age = age || 0;
  this.hobbies = ['music', 'reading'];
}

Person.prototype.say = function() {
  console.log('I am a person');
};

function Student(name, age) {
  Person.call(this, name, age);
  this.score = 80;
}

// 核心：因为是对父类原型的复制，所以不包含父类的构造函数，也就不会调用两次父类的构造函数造成浪费
extend(Student, Person);
Student.prototype.study = function() {
  console.log('I am studying');
};

// ------使用------
let stu1 = new Student('lucy', 6);
stu1.hobbies.push('math');

let stu2 = new Student('tom', 6);
stu2.hobbies.push('art');

console.log(stu1.name); // lucy
console.log(stu1.age); // 1
console.log(stu1.score); // 80 子类自己的属性

console.log(stu1.hobbies); // music,reading,math,art 共享引用属性
console.log(stu2.hobbies); // music,reading,math,art 共享引用属性

console.log(stu1.say === stu2.say);
```

#### ES6 Class extends

核心:

- ES6 继承的结果与寄生组合继承类似, 本质上, 是 ES5 继承的语法糖
