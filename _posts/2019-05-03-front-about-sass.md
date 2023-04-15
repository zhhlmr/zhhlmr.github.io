---
title: 前端 - About Sass/Scss
tags: 
 - Web
---

<!-- ![cover](https://images.unsplash.com/photo-1480506132288-68f7705954bd?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjMwNTA3fQ&auto=format&fit=crop&w=1393&q=80) -->

<!-- more -->
## 用法 


#### Variables (变量)

```
//sass style
//-----------------------------------
$fontStack:    Helvetica, sans-serif;
$primaryColor: #333;

body {
  font-family: $fontStack;
  color: $primaryColor;
}

//css style
//-----------------------------------
body {
  font-family: Helvetica, sans-serif;
  color: #333;
}


```


--- 

#### Nesting (嵌套)

```
//sass style
//-----------------------------------
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  li { display: inline-block; }

  a {
    display: block;
    padding: 6px 12px;
    text-decoration: none;
  }
}

//css style
//-----------------------------------
nav ul {
  margin: 0;
  padding: 0;
  list-style: none;
}

nav li {
  display: inline-block;
}

nav a {
  display: block;
  padding: 6px 12px;
  text-decoration: none;
}

```


#### Import (导入)

```
//sass style
//-----------------------------------
// _reset.scss

html,
body,
ul,
ol {
   margin: 0;
  padding: 0;
}

//sass style
//-----------------------------------
// base.scss 

@import 'reset';

body {
  font-size: 100% Helvetica, sans-serif;
  background-color: #efefef;
}

//css style
//-----------------------------------
html, body, ul, ol {
  margin: 0;
  padding: 0;
}

body {
  background-color: #efefef;
  font-size: 100% Helvetica, sans-serif;
}

```



#### Mixin (宏定义)

```
//sass style
//-----------------------------------
@mixin box-sizing ($sizing) {
    -webkit-box-sizing:$sizing;     
       -moz-box-sizing:$sizing;
            box-sizing:$sizing;
}
.box-border{
    border:1px solid #ccc;
    @include box-sizing(border-box);
}

//css style
//-----------------------------------
.box-border {
  border: 1px solid #ccc;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}
```


#### Extended/Inherited (扩展/继承)


```
//sass style
//-----------------------------------
.message {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

.success {
  @extend .message;
  border-color: green;
}

.error {
  @extend .message;
  border-color: red;
}

.warning {
  @extend .message;
  border-color: yellow;
}


//css style
//-----------------------------------
.message, .success, .error, .warning {
  border: 1px solid #cccccc;
  padding: 10px;
  color: #333;
}

.success {
  border-color: green;
}

.error {
  border-color: red;
}

.warning {
  border-color: yellow;
}
```


#### Operatior（运算符）

```
//sass style
//-----------------------------------
.container { width: 100%; }

article[role="main"] {
  float: left;
  width: 600px / 960px * 100%;
}

aside[role="complimentary"] {
  float: right;
  width: 300px / 960px * 100%;
}

//css style
//-----------------------------------
.container {
  width: 100%;
}

article[role="main"] {
  float: left;
  width: 62.5%;
}

aside[role="complimentary"] {
  float: right;
  width: 31.25%;
}

```


#### Color (颜色)


```
//sass style
//-----------------------------------
$linkColor: #08c;
a {
    text-decoration:none;
    color:$linkColor;
    &:hover{
      color:darken($linkColor,10%);
    }
}

//css style
//-----------------------------------
a {
  text-decoration: none;
  color: #0088cc;
}
a:hover {
  color: #006699;
}

```


## 高级用法

#### If/Else(条件语句)



```
p {
　　　　@if 1 + 1 == 2 { border: 1px solid; }
　　　　@if 5 < 3 { border: 2px dotted; }
　　}
　　
```


```
　@if lightness($color) > 30% {
　　　　background-color: #000;
　　} @else {
　　　　background-color: #fff;
　　}
　　
```



#### For/While (循环语句)


```
@for $i from 1 to 10 {
　　　　.border-#{$i} {
　　　　　　border: #{$i}px solid blue;
　　　　}
　　}
　　
```


```
$i: 6;
　　@while $i > 0 {
　　　　.item-#{$i} { width: 2em * $i; }
　　　　$i: $i - 2;
　　}

```

```
//each is similar with for 
@each $member in a, b, c, d {
　　　　.#{$member} {
　　　　　　background-image: url("/image/#{$member}.jpg");
　　　　}
　　}
　　
```


#### Function（自定义函数）


```
@function double($n) {
　　　　@return $n * 2;
　　}
　　#sidebar {
　　　　width: double(5px);
　　}
　　
```



### 参考



- [阮一峰 - SASS用法指南](http://www.ruanyifeng.com/blog/2012/06/sass.html)
- [Sass Basics](http://sass-lang.com/guide)
- [官方转换器](http://www.sassmeister.com/)
- [Another 转换器](http://css2sass.herokuapp.com/)
- [W3 - Sass Guide](http://www.w3cplus.com/sassguide/)
- [Sass基础教程](http://www.sasschina.com/guide/)
- [sass十分钟入门](http://www.w3cplus.com/sassguide/)

