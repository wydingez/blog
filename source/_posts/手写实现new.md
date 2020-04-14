title: 手写实现new
author: wyding
date: 2020-04-14 21:00:17
tags:
---
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