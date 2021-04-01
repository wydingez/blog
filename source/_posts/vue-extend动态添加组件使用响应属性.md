title: Vue.extend动态添加组件使用响应属性
author: wyding
tags:
  - vue
categories: []
cover: 'http://cdn.wyd94.top/82536482da758cf77be373c0bcee869e.jpeg'
date: 2021-04-01 15:25:00
---
> Vue.extend创建的组件，在使用时没法动态的绑定响应式参数，查阅了些资料后记录一下

<!-- more -->
## 如果要动态绑定父组件传递过来的props，可以使用如下方法：

```js
const Component = Vue.extend(parseStrToFunc);

const myComponent = new Component({})
// *这里需要这样绑定 设置属性 / 事件
myComponent._props = _props
myComponent._events = {
  input: [(val) => {
  	this.$emit('input', val)
  }]
}

this.component = myComponent.$mount();
```

## 参考

- [Dynamically add Vue component with reactive properties](https://forum.vuejs.org/t/dynamically-add-vue-component-with-reactive-properties/17360/6)