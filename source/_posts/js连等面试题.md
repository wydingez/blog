title: 'js连等赋值问题: a.x = a = {n: 2}'
author: wyding
tags:
  - js
categories:
  - 面试
date: 2020-02-26 11:28:00
---
> 最近看到一个面试题目：a.x = a = {n: 2}

<!-- more -->

```js
var a = {n: 1}
var b = a
a.x = a = {n: 2}    // <=>  a.x = a = {n: 2}

a.x 	// 这时 a.x 的值是多少
b.x 	// 这时 b.x 的值是多少
```

> 按照正常的逻辑去思考这个题目一般的想法是:
1）先把`{n: 2}`赋值给a
2）然后再创建a.x，将`{n: 2}`赋值给a.x
但是实际运行出来的结果与想法是不一样的～

事实上，解析器在拿到`a.x = a = {n: 2}`这样的语句后，会这样做：

1）找到`a`和`a.x`的指针。如果已有指针，那么不改变它。如果没有指针，即那个变量还没有被声明，那么就创建它，指向`null`。
`a`是有指针的，指向了`{n: 1}`；`a.x`没有指针，所以创建它，指向`null`
2）然后把上面找到的指针，都指向最右侧赋的那个值，即`{n: 2}`

所以执行后的流程如下图所示

![upload successful](/blog/images/pasted-0.png)

> 同时还要注意的是：
1）`.`运算优先于`=`赋值运算([运算符优先级MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Operator_Precedence))
2）`=`赋值运算是从右往左进行的