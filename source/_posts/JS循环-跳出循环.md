title: JS循环-跳出循环
author: wyding
tags:
  - js
categories: []
cover:
    https://wyd94-1302884909.cos.ap-nanjing.myqcloud.com/973a8a60848ec4014634fbb903feb68a.png
date: 2020-09-11 16:44:00
---
> 在js中的循环中需要跳出循环，正确的打开方式🙆

<!-- more -->
#### 1.最常用就是for循环，直接使用break
```javascript
var arr = [1, 2, 3, 4]
for (var i = 0; i < arr.length; i++) {
    if (...) {
        break;
    }
}
```

#### 2.使用forEach循环时，使用break无法跳出循环，这个时候可以使用try-catch来终止循环
```javascript
try {
    var arr = [1, 2, 3, 4]
    arr.forEach((item, index) => {
        if (...) {
            // break
            throw new Error('error')
        }
    })
} cathc (e) {
    console.log(e)
}
```

#### 3.使用es6的some方法来终止循环：some方法是用来遍历数组，当有一个元素满足条件时，直接返回true，同时会自动终止循环，因此可以利用这个特性来跳出循环
```javascript
var arr = [1, 2, 3, 4]
arr.some((item) => {
    if (...) {
        // break
        return true
    }
    return false
})
```