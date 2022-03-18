### JS继承
ECMAScript 的继承主要分为以下几种
#### 原型链继承
```js
function Parent() {
    this.name = 'parent'
}

Parent.prototype.getName = function(){
    return this.name
}

function Child() {
    this.childname = 'child'
}

Child.prototype = new Parent()

Child.prototype.getChildName = function() {
    return this.childname 
}

let child = new Child()
console.log(child.getName()) // parent

```
&emsp;原型链的继承方式 本质是重写原型对象，代之以一个新类型的实例。每当访问实例上的一个属性，他就会在原型链中寻找对应的属性，直到找到对应的方法或者属性， 当没有找到对应的属性时，会一环环找到原型链的终端为止

&emsp;存在问题：当原型包含引用类型的时候，会被所有实例 共享
```js
function Parent() {
  //...
  this.children = []
}

let child1 = new Child()
child1.children.push('Army')
let child2 = new Child()
child2.children  // ['Army']
```

#### 借用构造函数
```js
function Parent() {
    this.colors = ['red', 'yellow']
}

function Child() {
    Parent.call(this)
}

let child1 = new Child()
let child2 = new Child()
child1.colors.push('blue')

child1.colors // ['red', 'yellow', 'blue']

child2.colors //['red', 'yellow']

```
&emsp; 函数复用无从谈起； 父类原型中定义的方法 对于实例来说也是不可见的

#### 组合继承
```js
function SuperType(name) {
    this.colors = ['red']
    this.name = name
}

SuperType.prototype.sayName = function {
    return this.name
}

function SubType(name, age) {
    SuperType.call(this, name)
    this.age = age
}

SubType.prototype = new SuperType()
SubType.prototype.constructor = Subtype
let instance1 = new SubType()
let instance2 = new SubType()
```
&emsp; 组合继承避免了原型链和借用构造函数的缺陷，融合了他们的优点。 但是也存在调用了两次 SuperType 的问题

#### 原型式继承
```js
function object(o) {
    function F(){}
    F.prototype = o;
    return new F()
}
var superType = {
    colors: ['red'],
    name: '12'
}
var instance1 = object(superType)
var instance2 = object(superType)
instance1.colors.push('yellow')
instance2.colors // ['red', 'yellow']
```
&emsp; 要求要有一个对象作为另外一个对象的原型， 且存在和原型链一样的问题，引用类型的值会被共享。Object.create() 几乎用的是这个原理， 他包含了两个参数 Object.create(base[, extend]) ，extend 是定义额外属性的对象

#### 寄生式继承
```js
function createObj(ori) {
    let yuan = object(ori)
    yuan.appendFunc = () => {
        
    }
    return yuan
}
```
&emsp;寄生式继承是对原型式继承 进一步为对象添加函数。 但会因函数不能复用而降低效率。只要能返回对象 都适用于次模型

#### 寄生组合式继承

```js

function SuperType(name) {
    this.colors = ['red']
    this.name = name
}

SuperType.prototype.sayName = function {
    return this.name
}

function SubType(name, age) {
    SuperType.call(this, name)
    this.age = age
}

function createObj(subType, superType) {
    let proto = object(superType.prototype)
    proto.contructor = subType
    subType.prototype = proto
}

createObj(SubType, SuperType)

```
&emsp; 寄生式继承相较于组合继承 少了一次SuperType 的调用； 

