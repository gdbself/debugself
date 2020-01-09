---
title: JavaScript中__proto__ 和prototype的区别
tags:
  - __proto__
  - prototype
  - 原型链
  - 继承
url: 146.html
id: 146
categories:
  - JavaScript
date: 2018-03-08 18:08:35
---

本文参考了[https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001434499763408e24c210985d34edcabbca944b4239e20000](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001434499763408e24c210985d34edcabbca944b4239e20000)

JavaScript不区分类和实例的概念，js中只有实例，不存在类似C++中的类的概念；而JS是面向对象的，没有类的概念如何实现面向对象(如继承的功能）呢？JS是通过原型来实现面向对象编程，这里的原型，就大约等于C++/Java中类的概念
-----------------------------------------------------------------------------------------------------------------------

  
**\_\_proto\_\_：大约等于C++/Java中类的概念**，**下面使用Object.create()新建对象，来说明\_\_proto\_\_的含义**
--------------------------------------------------------------------------------------

Object.create()参数传入一个实例Student，返回一个新对象xiaoming，而新对象xiaoming的原型就是Student  

// 原型对象:
var Student = {
name: 'Robot',
height: 1.2,
run: function () {
    console.log(this.name + ' is running...');
}};

function createStudent(name) {
// 基于Student原型创建一个新对象:
var s = Object.create(Student);
// 初始化新对象:
s.name = name;
return s;
}

var xiaoming = createStudent('小明');
var xiaohong = createStudent('小红');
xiaoming.run(); // 小明 is running...
xiaohong.run(); // 小红 is running...
xiaoming.\_\_proto\_\_ === Student; // true，xiaoming的原型是Student
xiaohong.\_\_proto\_\_ === Student; // true，xiaohong的原型也是Student

上述代码中，xiaoming的原型是Student，xiaohong的原型也是Student，当我们给原型Student新增Student.run2()函数后，xiaoming，xiaohong都自动拥有run2函数  

Student.run2 = function(){
console.log(this.name + ' is running2...');
}

xiaoming.run2(); // 小明 is running2...
xiaohong.run2(); // 小红 is running2...

**prototype：专门为构造函数才引入的属性；下面****使用构造函数来新建对象，说明prototype的含义**

function Student(name) {
this.name = name;
this.hello = function () {
    alert('Hello, ' + this.name + '!');
    }
}

你会问，咦，这不是一个普通函数吗？  
这确实是一个普通函数，但是在JavaScript中，可以用关键字new来调用这个函数，并返回一个对象：  

var xiaoming = new Student('小明');
var xiaohong = new Student('小红');
xiaoming.name;     // '小明'
xiaoming.hello();  // Hello, 小明!

注意：  

如果不写new，这就是一个普通函数，它返回undefined。但是，如果写了new，它就变成了一个构造函数，它绑定的this指向新创建的对象，并默认返回this，也就是说，不需要在最后写return this;。  
这里使用构造函数来创建新的对象，而没有使用Object.create()，造成xiaoming.\_\_proto\_\_  != Student，可以通过下面的代码来验证

Student.run2 = function(){
    console.log(this.name + ' is running2...');
}
xiaoming.run2(); // TypeError: xiaoming.run2 is not a function
xiaohong.run2(); // TypeError: xiaohong.run2 is not a function

**上面我们为Student添加了run2函数，但是xiaoming和xiaohong并不能调用run2函数，所以xiaoming.\_\_proto\_\_  != Student**

**但是，我们却有以下事实：**

*   **xiaoming.\_\_proto\_\_ === xiaohong.\_\_proto\_\_;即xiaoming和xiaohong拥有相同的原型**
    
*   **xiaoming和xiaohong都是由构造函数Student构造而来**
    

**既然xiaoming和xiaohong来自Student，那么xiaoming和xiaohong的原型肯定和Student有关系，但是xiaoming和xiaohong的原型又不等于Student，那么该如何表示xiaoming和xiaohong的原型呢？**

****个人根据事实倒推，估计js的原型被创造时，为了表示xiaoming和xiaohong共同的原型，就为Student引入了prototype这个属性，用Student.prototype表示xiaoming和xiaohong的原型，即****

****xiaoming.\_\_proto\_\_ === xiaohong.\_\_proto\_\_ === Student.prototype****  

**请注意，在JS中，只有函数对象，即typeof(obj) == function的对象才有prototype属性，而其他类型的object是没有prototype属性的，但是所有类型的object都有\_\_proto\_\_属性的，这也可以印证上述的推测，即prototype是专门为构造函数才引入的属性，用来表示由该构造函数，构造出来的全部对象，所共有的那个原型**

JavaScript中\_\_proto\_\_ 和prototype的区别，总结以下

\_\_proto\_\_ 是所有对象都有的属性，obj.\_\_proto\_\_指向了obj的原型，注意是指向了原型，而不是原型本身；那么obj的原型本身是什么呢，有以下两种情况：

*   obj = Object.create(Student);此时obj的原型本身是Student
    
*   **obj = new Student('小明');此时obj的原型本身是Student.prototype**
    

* * *

![image.png](/images/wp/1523809861105734.png "1523809861105734.png")

在上图中“某个对象”，就是xiaohong.\_\_proto\_\_,也即Student.prototype，通过读写Student.prototype来修改原型；新增Student.prototype.run2()函数后，而xiaoming就拥有了run2函数

**通过prototype实现继承**
-------------------

如何通过原型链实现继承呢，比如从Student我想扩展出PrimaryStudent，这是需要使用到prototype了，代码稍微复杂，请参考https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/0014344997013405abfb7f0e1904a04ba6898a384b1e925000

**通过class和extends实现继承：比prototype更简洁**
-------------------------------------

后来ES6直接引入class和extends，这样不需要手动通过protype，而是直接通过class和extends就可以直接扩展出PrimaryStudent，当然，class和extends内部依旧是通过prototype实现的