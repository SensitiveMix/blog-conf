---
title: Javascript 设计模式与开发实践总结
date: 2018-02-26 15:13:59
categories:
  - 设计模式
tags:
  - Javascript
  - DesignPattern
---

# 2018.02.04
## 第一章：基础部分
### 1.1 多态类型语言和鸭子语言
* 鸭子类型：走起来像鸭子，叫起来也像鸭子，那么他就是一只鸭子。
### 1.2 多态
多态含义：同一操作作用于不同的对象上面，可以产生不同的解释和不同的执行结果。换句话说，给不同的对象发送同一个消息的时候，这些对象会根据这个消息分别给出不同的反馈。 
* 基本多态
```
var makeSound = function (animal) {
  if (animal instanceOf Duck) {
    console.log('嘎嘎嘎')
  } else if (animal instanceOf Chicken) {
    console.log('咯咯咯')
  }
}

var Duck = function() {}
var Chicken = function() {}

makeSound(new Duck())
makeSound(new Chicken())
```
优化多态
```
var makeSound = function (animal) {
  animal.sound()
}

var Duck = function() {}
Duck.prototype.sound = function() {
  console.log('嘎嘎嘎')
}

var Chicken = function() {}
Chiecken.prototype.sound = function() {
  console.log('咯咯咯')
}
```
* ES6 多态类
```
class MakeSound {
  sound (animal) {
    animal.sound()
  }
}

class Duck {
  sound () {
    console.log('嘎嘎嘎')
  }
}

class Chicken {
  sound () {
    console.log('咯咯咯')
  }
}

var makesound = new MakeSound()
var duck = new Duck()
var chicken = new Chicken()
makesound.sound(duck)
makesound.sound(chicken)
```
### 1.2.6 多态在面向对象中的设计
假设我们要编写一个地图应用，现在有两家可选的地图 API 提供商供我们接入自己的应用。 目前我们选择的是谷歌地图，谷歌地图的 API 中提供了 show 方法，负责在页面上展示整个地图。 示例代码如下 ：

```
var googleMap = { show: function(){

console.log( '开始渲染谷歌地图' ); }

};

var renderMap = function(){ googleMap.show();

};
renderMap(); // 输出:开始渲染谷歌地图 
```

后来因为某些原因，要把谷歌地图换成百度地图，为了让 renderMap 函数保持一定的弹性， 我们用一些条件分支来让 renderMap 函数同时支持谷歌地图和百度地图: 

```
var googleMap = { show: function(){

console.log( '开始渲染谷歌地图' ); }

};

var baiduMap = { show: function(){

console.log( '开始渲染百度地图' ); }

};

var renderMap = function( type ){ if ( type === 'google' ){ googleMap.show();

}else if ( type === 'baidu' ){ baiduMap.show();

} };

renderMap( 'google' ); // 输出:开始渲染谷歌地图 renderMap( 'baidu' ); // 输出:开始渲染百度地图 
```

可以看到，虽然 renderMap 函数目前保持了一定的弹性，但这种弹性是很脆弱的，一旦需要 替换成搜搜地图，那无疑必须得改动 renderMap 函数，继续往里面堆砌条件分支语句。 

优化实现：

```
class RenderMap {
  start (Map) {
    new Map().show()
  }
}

class BaiduMap {
  show () {
    console.log('开始渲染百度地图')
  }
}

class GoogleMap {
  show () {
    console.log('开始渲染谷歌地图')
  }
}

const rendermap = new RenderMap()
rendermap.start(BaiduMap)
rendermap.start(GoogleMap)
```
### 
### 1.4.1 使用克隆的原型模式
从设计模式的角度讲，原型模式是用于创建对象的一种模式，如果我们想要创建一个对象， 一种方法是先指定它的类型，然后通过类来创建这个对象。原型模式选择了另外一种方式，我们 不再关心对象的具体类型，而是找到一个对象，然后通过克隆来创建一个一模一样的对象。 

既然原型模式是通过克隆来创建对象的，那么很自然地会想到，如果需要一个跟某个对象一 模一样的对象，就可以使用原型模式。 

假设我们在编写一个飞机大战的网页游戏。某种飞机拥有分身技能，当它使用分身技能的时 候，要在页面中创建一些跟它一模一样的飞机。如果不使用原型模式，那么在创建分身之前，无 疑必须先保存该飞机的当前血量、炮弹等级、防御等级等信息，随后将这些信息设置到新创建的 飞机上面，这样才能得到一架一模一样的新飞机。 

```
  如果使用原型模式，我们只需要调用负责克隆的方法，便能完成同样的功能。
```

原型模式的实现关键，是语言本身是否提供了 clone 方法。ECMAScript 5 提供了 Object.create 方法，可以用来克隆对象。代码如下: 

```
Object.create = Object.create || function( obj ){ 
   var F = function() {} 
   F.prototype = obj 
   return new F() 
}

class Flane {
  constructor () {
    this.height = 100   
    this.width = 200
  }
}

var flane = new Flane()
flane.height = 101
flane.width = 201

console.log(flane)

const cloneFlane = Object.create(new Flane())
console.log(cloneFlane) 
```

# 2018.02.05
### 1.4.4 原型编程规范
* 所有数据都是对象
* 要得到一个对象，不是实例化一个类，而是找到一个对象作为原型并克隆它
* 对象会记住他的原型
* 如果对象无法响应某个请求，它会把这个请求委托给它的原型
## 第二章：this、apply 和 call
### 2.1.1 关于 this 的指向
* 作为对象的方法调用
```
var obj = { 
  a: 1,
  getA: function(){
      alert ( this === obj ); // 输出:true 
      alert ( this.a ); // 输出: 1
  } 
};

obj.getA(); 
```
* 作为普通函数调用
```
window.globalName = 'jack'

var map = function () {
  return this.globalName
}

console.log(map())
```
* 构造器调用
```
var MyClass = function () {
  this.name = 'jack'
}

var myclass = new MyClass()
console.log(myclass.name)
```
* Function.prototype.apply 和 Function.prototype.call
```
var object = {
  name: 'jack',
  getName: function () {
    return this.name
  }
}

var objectClone = {
  name: 'rose'
}

console.log(object.getName())
console.log(object.getName.call(objectClone))
```

### 2.2 call 和 apply
**关键在于传入参数的形式不同**

apply 接受两个参数，第一个参数指向 this 的指向，第二个参数作为一个带下标的集合，这个集合可以是个数组，也可以是一个类数组，apply 方法将这个参数传递给被调用的函数：

```
var func = function (a, b, c) {
  console.log([a, b, c])
}

func.apply(null, [1, 2, 3])
```

call 接受多个参数，第一个参数指向 this 的指向，后面的参数依次传递给被调用的函数

```
var func = function (a, b, c) {
  console.log([a, b, c])
}
func.call(null, 1, 2, 3)
```

**用途**
* **改变 this 指向**
* **Function.prototype.bind**
```
Function.prototype.bind = function(){ 
  var self = this, // 保存原函数
  var context = [].shift.call( arguments ),
  var args = [].slice.call( arguments ); 
  return function(){ // 返回一个新的函数
  // 需要绑定的 this 上下文 // 剩余的参数转成数组
    return self.apply( context, [].concat.call( args, [].slice.call(   arguments ) ) ); // 执行新的函数的时候，会把之前传入的 context 当作新函数体内的 this
// 并且组合两次分别传入的参数，作为新函数的参数
} };

var obj = { 
  name: 'sven'
};

var func = function( a, b, c, d ){
alert ( this.name ); // 输出:sven
alert ( [ a, b, c, d ] ) // 输出:[ 1, 2, 3, 4 ]

}.bind( obj, 1, 2 ); func( 3, 4 ); 
```
* **借用其他对象的方法**
```
var a = function (name) {
  this.name = name
}

var B = function () {
  a.apply(this, arguments)
}

B.prototype.getName = function () {
  return this.name
}

var b = new B('hello')
console.log(b.getName())
```
# 2018.02.07
## 第二部分：设计模式
* 单例模式

单例模式的定义：保证一个类仅有一个实例，并提供唯一访问它的全局访问点。

# 2018.02.10
* 单例模式实例
```
var createLoginLayer = function(){
  var div = document.createElement( 'div' )
  div.innerHTML = '我是登录浮窗'
  div.style.display = 'none'
  document.body.appendChild( div )
  return div
}

var createSingleLoginLayer = getSingle( createLoginLayer )

document.getElementById( 'loginBtn' ).onclick = function(){ 
  var loginLayer = createSingleLoginLayer()
  loginLayer.style.display = 'block'
}

下面我们再试试创建唯一的 iframe 用于动态加载第三方页面:

var createSingleIframe = getSingle( function() {
  var iframe = document.createElement ( 'iframe' )
  document.body.appendChild( iframe )
  return iframe
})

document.getElementById( 'loginBtn' ).onclick = function(){ 
  var loginLayer = createSingleIframe()
  loginLayer.src = 'http://baidu.com'
```
# })

2018.02.12
## 第五章： 策略模式
定义一系列算法，把他们封装起来，并且使他们可以相互替换。

### 5.1.3 实例代码
```
var financeA = function () {}

financeA.prototype.calculate = function (salary) {
  return salary * 2
}

var financeB = function () {} 
financeB.prototype.calculate = function (salary) {
  return salary * 3
}

var financeC = function () {}
financeC.prototype.calculate = function (salary) {
  return salary * 4
}

var Bonus = function () {
  this.stragety = null
  this.salary = null 
}

Bonus.protoType.setStragety = function (stragety) {
  return this.stragety = stragety
}

Bouns.protoType.getBonus = function (stragety) {    // 取得奖金数额
  this.setStragety(stragety).calculate(this.salary) // 将计算奖金操作委托给对应的策略对象
}
```

### 5.7 策略模式的优缺点
  * 策略模式利用组合和委托和多态技术的思想，可以有效的避免多重条件选择条件。
  * 策略模式提供了对开放-封闭原则的完美支持，将算法封装到 strategy 中，使得他们易于切换，易于理解，易于扩展。
  * 策略模式的算法也可以复用在系统其它地方，从而避免许多重复的复制粘贴工作。
  * 在策略模式中，利用组合和委托实现了 context 执行算法的能力，这也是继承一种更轻便的实现方案。
### 
## 第六章：代理模式
### 6.2 虚拟代理
虚拟代码就是把开销很大的对象，留存到需要它的地方再去创建。
### 6.8 缓存代理
缓存代理可以为一些开销大的运算结果提供暂时的存储，可用于 AJAX 请求对象
### 6.10 其他代理模式
* 保护代理

保护代理用于控制不同权限的对象对目标对象访问
* 防火墙代理

控制网络资源的访问
* 远程代理

为一个对象在不同地址空间提供局部代表
* 智能引用代理

取代简单的指针，在对象访问的时候提供一些特殊的操作，比如计算一个对象被引用的次数
* 写时复制代理

通常用于复制一个庞大的对象，写时复制延迟了复制的过程。当对象真正被修改的时候，才去进行复制操作。写时复制是虚拟代理的一种变体，DLL （操作系统的动态链接库）是典型的运用场景
# 2018.02.26
## 享元模式：解决性能问题而生
适用场景：
* 一个程序中使用了大量重复对象
* 由于使用大量对象，造成内存开销
* 对象的大对数状态多可以变成外部状态
* 剥离出对象的外部状态之外，可以用较少的共享对象取代大量对象

对象池：数据库连接对象，HTTP连接池适用场景

