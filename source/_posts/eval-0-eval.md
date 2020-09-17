title: 'eval & (0, eval)'
author: wyding
tags:
  - js
categories: []
date: 2019-11-01 10:31:00
cover: https://cdn.wyd94.top/9d888ef6a19412df883faff2f86a9ae7.jpeg
---
> 最近看到`eval`和`(1, eval)`这个东西，乍一看一脸懵逼，查了下文档，特此记录一下，[参考连接](https://stackoverflow.com/questions/9107240/1-evalthis-vs-evalthis-in-javascript/9107367#9107367)

<!-- more -->
## 1. js逗号运算符
说到`(1, eval)`，先了解下逗号运算符。逗号运算符，它将**先计算左边的参数，再计算右边的参数值。然后返回最右边参数的值**
```js
var a = 10
var b = 20

function test () {
	return a++, b++, 10
}

var c = test()
alert(a)		// 11
alert(b)		// 21
alert(c)		// 10
```

## 2. eval VS (1, eval)
Q1: 
```js
(1, eval) === eval
```		
Q2: 
```js
var global = (function () {
    return this || eval('this');
}());

?
=>

var global = (function () {
    return this;
}());
```


`(1, eval)`和原型`eval`的区别在于前者是一个**值**，而后者是一个**左值**		
左值：是指表达式只能出现在赋值运算符的左侧，在js中,变量,对象属性和数组元素均是左值		

`(1, eval)`是一个表达式来声明eval（也就是说，就像是`(true, eval)`, `(0 ? 0 : eval)`）		

ECMA认为`eval`的引用是一个**直接引用**，但是表达式却被认为试试`eval`的一个`间接引用`（`间接引用确保执行在全局作用域下`）		


也许你会困扰以下问题：
1. 在什么情况下直接引用eval不在全局作用域上？
2. 在什么情况下，在全局中用于下的function中的this不被认为是全局对象？

对于问题1，答案是大多数情况下，直接引用`eval`的执行在当前的运行的作用域下
```js
var x = 'outer';
(function() {
  var x = 'inner';
  eval('console.log("direct call: " + x)'); 
  (1,eval)('console.log("indirect call: " + x)'); 
})();

// direct call: inner
// direct call: outer
```

对于问题2，`this` cannot be set to `null` or `undefined`