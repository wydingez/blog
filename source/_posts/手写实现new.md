title: 面试相关
author: wyding
date: 2020-04-14 21:00:17
tags:
---
> 记录一些容易忘记的面试相关的东西
<!-- more -->

#### 1. new的手动实现
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

#### 2. call/apply/bind：函数执行改变this
 - `call`：fn.call(target, 1, 2)
 - `apply`：fn.apply(target, [1, 2])&nbsp;&nbsp;&nbsp;&nbsp;**// 可以这样记：apply的a对应数组Array**
 - `bind`: fn.bind(target)(1, 2)&nbsp;&nbsp;&nbsp;&nbsp;**// 可以这样记：bind对应绑定，绑定完后再执行调用**
 
#### 3. 闭包
闭包是指父级作用域被销毁的情况下，返回出的子函数仍然保留父级变量对象和作用域链，因此可以继续访问父级的变量对象

- 闭包会产生一个问题：
 - 多个子函数同时指向父级，是完全共享的，修改了一个，其他都会受到影响
- 解决：
 - 变量使用函数参数传递的方式，避免向上查找
 - 使用setTimeout包裹，通过第三个参数传递
 - 使用块级作用域，例如let