title: 面试相关-JS
author: wyding
cover: >-
  https://cdn.wyd94.top/2c0c82a61b922fd07a3ffc455e564987.jpg
date: 2020-04-14 21:00:17
tags:
---
> 记录一些容易忘记的面试相关的东西-js
<!-- more -->

## 1. new的手动实现
```js
function create () {
	// 1. 创建一个空对象
    let obj = new Object()
    // 2. 获取构造函数
    let Constructor = [].shift.call(arguments)
    // 3. 实例的__proto__指向构造函数的原型
    obj.__proto__ = Constructor.prototype
    // 4. 绑定Constructor的this到实例上
    let result = Constructor.apply(obj, arguments)
    // 5. 有返回值，则返回相应的返回值
    return typeof result === 'object' ? result : obj
}
```

## 2. call/apply/bind：函数执行改变this
 - `call`：fn.call(target, 1, 2)
 - `apply`：fn.apply(target, [1, 2])&nbsp;&nbsp;&nbsp;&nbsp;**// 可以这样记：apply的a对应数组Array**
 - `bind`: fn.bind(target)(1, 2)&nbsp;&nbsp;&nbsp;&nbsp;**// 可以这样记：bind对应绑定，绑定完后再执行调用**
 
## 3. 闭包
闭包是指父级作用域被销毁的情况下，返回出的子函数仍然保留父级变量对象和作用域链，因此可以继续访问父级的变量对象

- 闭包会产生一个问题：
 - 多个子函数同时指向父级，是完全共享的，修改了一个，其他都会受到影响
- 解决：
 - 变量使用函数参数传递的方式，避免向上查找
 - 使用setTimeout包裹，通过第三个参数传递
 - 使用块级作用域，例如let
 
## 4. 常使用的的库有哪些？常用的前端开发工具？开发过什么应用或者组件？
- 常用的库：echarts/datejs/lodash
- 开发工具：vscode/git
- 开发组件：通用查询

## 5. 什么是Javascript原型，原型链？
- 原型：一个简单的对象，用于实现属性的继承
- 原型链：由原型对象组成，用来实现继承和共享属性的有限对象链

## 6. HTML5的离线存储是什么？
允许web应用在没有网络的情况下正常使用，通过在html中引入manifest文件来实现缓存的功能。现在已经不太推荐使用了，推荐使用Service Workers来替代。

## 7. iframe有哪些缺点？
- 搜索引擎不够友好
- 手机兼容性不够好
- 响应式依赖于iframe的宽高，不再依赖于浏览器的window

## 8. eval是做什么的？
计算某个字符串，并执行字符串中的js代码，不推荐使用，有安全隐患

## 9. node.js的运用场景
- Restful API
- 统一Web应用的UI层
- 大量ajax请求的应用

## 10. web应用如何从服务器推送data到客户端有哪些方式？

## 11. vue/react/angular三种框架的比较

## 12. vue虚拟dom的实现原理

## 13. 手写实现promise

## 14. 手写实现防抖和节流

## 15. 浏览器跨域的原理，以及跨域的几种方式