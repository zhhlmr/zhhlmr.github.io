---
title: Javascript - 关于 apply, call, bind, this
categories: [Programming, Front-end]
tags: 
 -  Web
---

<!-- ![cover](https://images.unsplash.com/photo-1555999017-0d0f80510719?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1950&q=80) -->

<!-- more -->
### 关于 apply, call, bind, this

这个话题在网上的讨论有很多，本文仅为浅析及重点备注。

apply，call，bind 三者的主要作用是为了改变函数中使用的this的指向。

那么问题来了：



### 为什么要改变this 的指向呢？

这就谈论到了ES5了，在ES5中，**this的指向，始终指向最后调用它的对象**。 

如：

```javascript
var name = "windowsName";
function tFun() {
    var name = "Cherry";
    console.log(this.name);          // windowsName
    console.log("inner:" + this);    // inner: Window
}
tFun();
console.log("outer:" + this)         // outer: Window

//tFun() 相当于是隐式调用 window.tFun() 所以最后调用的对象是 window。


 var name = "windowsName";
 var obj = {
        name: "Cherry",
        fn : function () {
            console.log(this.name);      // Cherry
        }
 }
 obj.fn();

// obj.fn() 为 对象obj 调用，所以log 的是 Cherry
```



以上为使用对象调用时的方式， 那么我们知道对象是可被赋值/调用的。

被赋值或调用时，会出现什么状况：

```javascript
var name = "windowsName";
var obj = {
    name : null,
    // name: "Cherry",
    fn : function () {
        console.log(this.name);      // windowsName
    }
}

var another = obj.fn;
another();
```

当对象obj的函数fn赋值到another 时，尚未被 调用，another() 相当于 window.another()，所以this指向的最后调用它的对象应该是 window



### 怎样改变this的指向呢？



- 使用ES6中的箭头函数
  - 箭头函数的 this 始终指向函数定义时的 this，而非执行时。
- 临时变量赋值 _self = this
- 使用apply、call、bind
- new 一个对象



### 如何使用apply,call,bind？



```javascript
var name = 'windowname';
var obj = {
    name : 'objname',
    objFunApply: function(){

     setTimeout(function(){
          console.log(this.name)
     }.apply(obj),100)
    },
    objFunCall: function(){
        setTimeout(function(){
          console.log(this.name)
        }.call(obj),100)
    },
    objFunBind: function(){
      setTimeout(function(){
        console.log(this.name)
      }.bind(obj),100)
   }
      
}
obj.objFunApply()
obj.objFunCall()
obj.objFunBind()
```





### apply,call,bind的区别



#### apply的调用方式：

```javascript
fun.apply(thisArg, [argsArray])
```

- thisArg 为指定的this
- argsArray 为参数数组



#### call 的调用方式：

```
fun.call(thisArg[, arg1[, arg2[, ...]]])
```

- thisArg 为指定的this
- arg1 , arg2,…. argn 为 若干个参数



### bind 的调用方式：

- 按照MDN的说明: bind()方法创建一个新的函数, 当被调用时，将其this关键字设置为提供的值，在调用新函数时，在任何提供之前提供一个给定的参数序列。



如：

```javascript
var name = "windowname";
var obj = {
  name: "objname",
  objFun: function(value1, value2) {
    console.log(value1, value2);
  }
};

var another = obj.objFun;
another.apply(obj, [1, 2]);
another.call(obj, 1, 2);
another.bind(obj,1,2)()
```






<!-- 
### 参考：



- [this、apply、call、bind](https://juejin.im/post/59bfe84351882531b730bac2#heading-4)

- [JavaScript 中至关重要的 Apply, Call 和 Bind](https://hijiangtao.github.io/2017/05/07/Full-Usage-of-Apply-Call-and-Bind-in-JavaScript/)

- [深入浅出妙用 Javascript 中 apply、call、bind]([http://web.jobbole.com/83642/](http://web.jobbole.com/83642/)) -->

  