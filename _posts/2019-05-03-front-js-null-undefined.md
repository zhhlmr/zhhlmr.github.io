---
title: Javascript - 关于 null, undefined, 0, [], true, false, void
categories: [Programming, Front-end]
tags: 
 -  Web

comments: true
---

<!-- ![cover](https://images.unsplash.com/photo-1484807352052-23338990c6c6?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1950&q=80) -->

<!-- more -->

- ECMAScript中的数据类型: 
  - 原始类型： Undefined、Null、Boolean、Number 、 String、[Symbol](https://developer.mozilla.org/en-US/docs/Glossary/Symbol)(ECMAScript 6 新定义，类似于C中的枚举类型)
  - 复杂数据类型：Objet
- null 是 Object
- undefined == null //true (undefined 派生自null)
- 0 == '' // true
- [] == false // true 
- 最初设计中，null是一个表示"无"的对象，转为数值时为0；undefined是一个表示"无"的原始值，转为数值时为NaN。
- **null表示"没有对象"，即该处不应该有值**
- **undefined表示"缺少值"，就是此处应该有一个值，但是还没有定义**
- null  可理解为一个空指针，尚未指向任何对象。
- 判断变量是否为undefined  : 
  - 使用 === 或者 !== , 当使用普通相等符== 时，会同时检查是否等于null
  - 使用typeof操作符
- 判断变量是否为null:
  - 使用 === 或者 !==
  - 不可使用typeof ， typeof null == 'object' // true
- void 运算符 对给定的表达式进行求值，然后返回 undefined



```javascript
if (!undefined) 
    console.log('undefined is false');
// undefined is false

if (!null) 
    console.log('null is false');
// null is false

undefined == null
// true

Object.getPrototypeOf(Object.prototype)
// null

var i;
i // undefined

function f(x){console.log(x)}
f() // undefined

var  o = new Object();
o.p // undefined

var x = f();
x // undefined

var value;

console.log(typeof data); // "undefined"
console.log(typeof value); // "undefined"
console.log(data === undefined); //error


var data;
console.log(data === void 0); //true
```



<!-- #### 参考

- [阮一峰: undefined与null 的区别]([http://www.ruanyifeng.com/blog/2014/03/undefined-vs-null.html](http://www.ruanyifeng.com/blog/2014/03/undefined-vs-null.html))
- [JavaScript深入理解之undefined与null](https://juejin.im/post/5aa4f7cc518825557e780256)
- [探索JavaScript中Null和Undefined的深渊](https://yanhaijing.com/javascript/2014/01/05/exploring-the-abyss-of-null-and-undefined-in-javascript/) -->

