title: 模拟vue-router中的beforeRouteLeave方法实现
author: wyding
tags:
  - vue
  - vue-router
  - promise
categories:
  - 学习笔记
cover: >-
  https://wyd-mages.oss-cn-shanghai.aliyuncs.com/a75470ce1ae6c2cc30b0f6429bc1c59a.jpeg
date: 2019-08-28 10:08:00
---
> 业务中遇到以下场景：在用户离开填写表单的页面，如果用户有修改内容，需要提示用户是否确认离开。`window.confirm()`是一个同步的方法，自然没有问题；但是项目中使用`iview`的`modal`组件，需要在点击确认时继续，取消时停止后续操作。

<!-- more -->
## 1.`vue-router`中我们常常会使用如下代码
```js
beforeRouteLeave (to, from, next) {
    if (this.currentTab === 'approver') {
        this.$Modal.confirm({
            title: '提示',
            content: '还有内容没有保存，确认离开吗？',
            onOk () {
                next()
            },
            onCancel () {
                next(false)
            }
        })
    } else {
        next()
    }
}
```
这个时候我们会考虑怎么实现一个这种使用`next`使得异步操作的代码能够有顺序的执行，即`next`控制了后面代码的执行


## 2.因此实现了如下的一个简单`demo`，主要是利用`Promise`的思想，即新创建的`Promise`处于`pending`状态，只有调用`resolve`函数才会向下继续执行 **(对以后封装组件很有参考价值)**
```js
function fun (next) {
    console.log('inner function')
    new Promise((resolve, reject) => {
        next((goOn) => {
            if (goOn === false) {
                reject()
            } else {
                resolve()
            }
        })
    }).then(() => {
        // 等等前面的程序再执行
        console.log('After 3 secondes, then run others...')
    })
}
fun((next) => {
    setTimeout(() => {
        let flag = true
        if (flag) {
            next()
        } else {
            next(false)
        }
    }, 3000)
})
// ------------output-----------------
inner function
1s...
2s...
3s...
After 3 secondes, then run others...
```