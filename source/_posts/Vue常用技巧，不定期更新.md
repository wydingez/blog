title: Vue常用技巧，不定期更新
author: wyding
tags:
  - vue
categories: []
cover:
  https://wyd94-1302884909.cos.ap-nanjing.myqcloud.com/timg.jpg
date: 2020-09-11 17:23:00
---
> 记录工作中使用vue遇到的问题，以及学到一些小技巧🤣

<!-- more -->

## 1. 路由的项目启动页和404页面
```javascript
export default new Router({
  routes: [
    {
      path: '/', // 项目启动页
      redirect:'/login' // 重定向到下方声明的路由
    }, {
      path: '*', // 404 页面
      component: () => import('./notFind') // 或者使用component也可以的
    }
  ]
})
```

## 2. **router-view**同一页面组件切换时，不会触发**created/mounted**
> 同一个**component**,默认情况下当这两个页面切换时并不会触发vue的**created**或者**mounted**钩子，官方说你可以通过**watch $route**的变化来做处理，但其实说真的还是蛮麻烦的。后来发现其实可以简单的在 **router-view**上加上一个唯一的**key**，来保证路由切换时都会重新渲染触发钩子了

```html
<router-view :key="key"></router-view>
```

```javascript
computed: {
  key() {
    return this.$route.name !== undefined? this.$route.name + +new Date(): this.$route + +new Date()
  }
}
```

## 3. vue的extend方法，下如何动态改变模板中的data属性
> 最近在使用Vue.extend动态创建组件时，需要动态给创建的组件传递变量参数，原本想使用provide/inject，但是在vue.extend模式下，创建，内部没法接收到。因此，后面差了相关资料，特此来记录下，[参考](https://segmentfault.com/q/1010000016082860)

```javascript
const Component = Vue.extend(parseStrToFunc);

this.component = new Component().$mount();

if (this.portlet?.itemStyle?.height) {
    // 动态设置参数height
    this.$set(this.component, 'height', this.portlet.itemStyle.height)
}
```

```javascript
watch: {
    'portlet.itemStyle': {
        handler (val) {
            if (this.component) {
                this.$set(this.component, 'height', val.height)
            }
        },
        immediate: true
    }
}
```

## 参考 

- [手摸手，带你用vue撸后台](https://juejin.im/post/59097cd7a22b9d0065fb61d2)