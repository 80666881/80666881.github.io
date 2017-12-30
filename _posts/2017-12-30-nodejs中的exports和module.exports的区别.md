---
title: nodejs中的exports和module.exports的区别
date: 2017-12-30
description: node新手常遇到的问题
categories:
 - node
 - js
photos: http://oxpvyb4d5.bkt.clouddn.com/nodejs.png
tags: node js
---

## 前言

在node的学习中，我们必定会遇到exports和module.exports这两个导出模块的方法，那到底有什么区别？我们究竟用哪个呢？

实际我们使用过程中，这两个方法一般都可以共用的。

---

## 举个栗子🌰

比如下面的例子。

```
//exports.js
module.exports.arr = [1,2,3]
```

```
//import.js
var obj = require('./exports');
var arr = obj.arr;
console.log(arr)
```
运行结果是:
![](http://oxpvb4fav.bkt.clouddn.com/15146109642764.jpg)

---

我们再改成exports

```js
exports.arr = [1,2,3]
```

一样正常获取到arr数组。

![](http://oxpvb4fav.bkt.clouddn.com/15146109642764.jpg)

---

## 不同之处

实际上，exports是node提供的一个上下文对象，用于方便我们导出模块，而这个exports指向的对象正是module.exports，所以三者的关系是这样的。

![](http://oxpvb4fav.bkt.clouddn.com/15146114379480.jpg)

又由于我们知道，js中的对象是按引用传递的，所以实际上exports和module.exports都指向同一个地址。

就像这样

![](http://oxpvb4fav.bkt.clouddn.com/15146115045635.jpg)

所以才会出现我们正常情况下使用```exports```和```module.exports```没有区别，因为都指向同一个object，我们在exports中修改属性，module.exports也能获取到。

---

## 不要改写module.exports

但还是有细微区别的。

比如我们改写了module.exports的情况下，比如：

```js
//exports.js
module.exports = [1,2,3]
exports.arr = [4,5,6]
```

```js
//import.js
var obj = require('./exports');
console.log(obj)
```

我们得出的结果只是module.exports的内容，而在exports上挂载的内容却完全获取不到了。

![](http://oxpvb4fav.bkt.clouddn.com/15146121128056.jpg)

一旦我们将```module.exports```进行重新改写的时候，它的指向也发生改变了，比如新的内存地址```0x0045```,那原来的```exports```还指向```0x0023```,而node只认```module.exports```指向的对象。

这就是我们一般不改写```module.exports```，而直接在上面挂载属性的原因。

因为一旦出现```module.exports```和```exports```混用的情况，你把```module.exports```改写了，```exports```上所有的内容就都无法获取到了。
![](http://oxpvb4fav.bkt.clouddn.com/15146120902373.jpg)

**当然，由于exports引用的是module.exports，所以更加不可以直接改变**

---

## 改进

假如我们通过属性挂载的方式进行exports，会是怎么样呢?

```js
module.exports.arr1 = [1,2,3]
exports.arr2 = [4,5,6]
```

![](http://oxpvb4fav.bkt.clouddn.com/15146122143055.jpg)

可以看到，这两个方法挂载的内容就都可以正常获取到了。


---

## 由此引发的一道题目的思考

之前在网上看到一道题，想起跟这个js的按值引用或者按传递引用有关系，题目如下:

```js
var a = {n:1}
var b = a
a.x = a = {n:2}
console.log(a.x)
console.log(b.x)
```

结果是多少呢？

```js
a.x//undefined
b.x//{n:2}
```
![](http://oxpvb4fav.bkt.clouddn.com/15146133186513.jpg)


让我们分析一下过程

 - 首先a指向一个地址比如0x0023，里面有对象```{n:1}```

 - 然后b也指向这个地址，0x0023

 - 之后由于```.```的运算级别高，先运算，所以就有0x0023里面的对象变成```{n:1,x:undefined}```

 - 然后a又指向了新的对象```{n:2}```，假如这个地址是0x0045

 - 然后a.x = a实际上指的是，0x0023里的对象```{n:1,x:undefined}```上，把```{n
:2}```赋值给了x

这样就变成

```js
b指向{n:1,x:{n:2}}//0x0023
a指向{n:2}//0x0045
```

结果自然一目了然.

所以我们要注意区分，当一个变量整个获取某个新的对象时，表示它的引用整个发生了改变。

那么原来的对象发生的改变自然无法同步到新对象上来。

## 《深入浅出nodejs》的解释

在《深入浅出nodejs》中，对于这个是这么解释的：
>exports对象是通过形参的方式传入的，直接赋值形参会改变形参的引用，但并不能改变作用域外的值。

怎么理解呢？
由于node的模块是经过包装的，经过包装后的模块大概是这样子

```js
(function(exports,require,module,__filename,__dirname){
    ...
})
```

这里我们就可以清楚地看到exports是作为形参传进去的。

如果我们直接改变exports，那么其```实参```是不会改变的。

由于书上的例子我觉得不够好，我再写一个这样的例子。

```js
var change2 = function (a) {
    a = {name:'atoms'}
   /*
    * 相当于：
    * var a;
    * a = obj = {name:'zeller'}
    * a = {name:'atoms'}
    * console.log(a)
    * 此时外面的obj指向的地址是不变的。
    */
    console.log(a);//{name:'atoms'}
}

var obj = {name:'zeller'}

change2(obj)

console.log(obj);//{name:'zeller'}
```

所以我们改变需要改变obj的属性时，都需要在其属性上改变，而不是直接改变整个对象（会改变指向）

```js
//改变属性，不是整个对象
var change3 = function (a) {
    a.name ='atoms'
    console.log(a);//{name:'atoms'}
}

var obj3 = {name:'zeller'}

change3(obj3)

console.log(obj3);//{name:'atoms'}
```

## 总结

在node中导出模块，我们可以写```module.exports```，也可以简写为```exports.xxx```
但混用的时候不要改写```module.exports``` 对象,因为```module.exports```就不等于```exports```,而我们require引用的就是```module.exports```，当我们混用的时候，exports上的属性就会无法获取。


当然，如果你清楚地知道只导出某个变量或方法，可以```module.exports = function(){}```这样写。


**更加不要直接修改```exports对象```，因为这样会导致```exports```不能指向```module.exports```**

我们可以通过挂载对象```module.export.xx```,
```exports.xx```
的方式导出。

**另外我们要注意，对象整个改写的时候，指向的地址是发生了改变的，注意新旧地址之间的变化，导致变量的不同，最好的例子就是上面的例题。**

以上，感谢阅读~~




