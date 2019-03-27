---
title: js继承
date: 2019-03-26 22:16:41
tags: [继承,原型]
---
> es5实现js继承的几种方式
<!-- more -->

# js继承


```js
//父类构造函数
function Person(name){
    this.name = name
}
Person.prototype.say = function (word){
    console.log(word)
} 
```
## 原型链继承
```js
//子类
function Man(){
    this.sex = "male"
}
// 子类原型指向父类实例
Man.prototype = new Person("jack")
Man.prototype.constructor = Man;
var man = new Man()
console.log(man.name) //jack
console.log(man.sex) //male
// man 的原型链上 找到 Person.prototype
console.log(man instanceof Person) //true
```
特点
1. 实例可继承的属性有：实例的构造函数的属性，父类构造函数属性，父类原型的属性。（新实例不会继承父类实例的属性！）
缺点
1. 新实例无法向父类构造函数传参
2. 继承单一
3. 所有新实例都会共享父类实例的属性。（原型上的属性是共享的，一个实例修改了原型属性，另一个实例的原型属性也会被修改！）


## 组合继承（组合原型链继承和借用构造函数复制父类构造函数属性）（常用）
```js
function Man(name){
    //复制父类构造函数的属性 
    Person.call(this,name);
    this.sex = "male"
}
//继承父类原型
Man.prototype = new Person()
Man.prototype.constructor = Man;
var man = new Man("jack")
console.log(man.name) //jack
console.log(man.sex) //male
console.log(man instanceof Person) //true
```
特点
1. 可以继承父类原型上的属性，可以传参，可复用
2. 每个新实例引入的构造函数属性是私有的
缺点
1. 调用了两次父类构造函数（耗内存），子类的构造函数会代替原型上的那个父类构造函数

## 寄生组合式继承
```js
// 子类构造函数 复制 父类构造函数 的属性
function Man(name){
    Person.call(this,name)
    this.sex = "male"
}
// 空函数F
function F(){}
// 把F的原型指向 父类的原型
F.prototype = Person.prototype
// 让子类的原型 指向F实例 从而利用原型链 继承父类的原型
Man.prototype = new F()
// 把Man 原型的 构造函数 修复为 Man
Man.prototype.constructor = Man

var man = new Man("jack")
console.log(man.name)
console.log(man.sex)
console.log(man instanceof Man)
console.log(man instanceof Person)
man.say("hello")
```
1. 修复了 组合继承调用两次父类构造函数的问题

### 代码简化
```js
function Person(name){
    this.name = name
}
Person.prototype.say = function (word){
    console.log(word)
} 
function inherits(Child, Parent){
    var F = function () {};
    F.prototype = Parent.prototype;
    Child.prototype = new F();
    Child.prototype.constructor = Child;
}
function Man(name){
    Person.call(this,name)
    this.sex = "male"
}
//实现继承
inherits(Man,Person)
```


## 注意点
```js
Man.prototype.constructor = Man
```
> 1. 由于Man的protoType被Person实例覆盖 所以Man.prototype.constructor 会指向 Person构造函数
> 2. 为了防止类似 (实例)man.__proto__.constructor() 等显式调用 返回的错误指向问题


> The constructor property makes absolutely no practical difference to anything internally. It's only any use if your code explicitly uses it. For example, you may decide you need each of your objects to have a reference to the actual constructor function that created it; if so, you'll need to set the constructor property explicitly when you set up inheritance by assigning an object to a constructor function's prototype property, as in your example.



