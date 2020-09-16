title: Vue常用技巧，不定期更新
author: wyding
tags:
  - vue
categories: []
cover: 'https://wyd94-1302884909.cos.ap-nanjing.myqcloud.com/timg.jpg'
date: 2020-09-11 17:23:00
---
> 记录工作中使用vue遇到的问题，以及学到一些小技巧🤣

<!-- more -->
{% meting "5039560" "netease" "song" "autoplay" "mutex:false" "listmaxheight:340px" "preload:none" "theme:#ad7a86"%}

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
> 同一个**component**，默认情况下当这两个页面切换时并不会触发vue的**created**或者**mounted**钩子，官方说你可以通过**watch $route**的变化来做处理，但其实说真的还是蛮麻烦的。后来发现其实可以简单的在 **router-view**上加上一个唯一的**key**，来保证路由切换时都会重新渲染触发钩子了

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

## 4. **hookEvent**的使用
**Vue**的生命周期，例如**created/mounted**等等，就是开发者规定好的一些**hook**，我们可以借此在不同的生命周期中去处理相应的业务逻辑。而在**Vue**源码中，则被称之为**hookEvent**

项目场景：
一个第三方的表格组件，渲染比较慢，为了用户更好的体验，可以加一个**loading**效果，但是如果想要加个**loading**效果，可能需要修改源码。这样带来的后果就是后续组件的升级需要自己去维护，总之这样看下来，不够优雅，也不够友好。有没有比较好的方式呢？这个时候就可以考虑到**hookEvent**了

可以先看看**vue**的生命周期函数源码实现：
```js
vm._self = vm
initLifecycle(vm)
initEvents(vm)
initRender(vm)
callHook(vm, 'beforeCreate')
initInjections(vm) // resolve injections before data/props
initState(vm)
initProvide(vm) // resolve provide after data/props
callHook(vm, 'created')
```
- 可以看到上面的源码，生命周期函数是通过**callHook**来调用的

```js
function callHook (vm: Component, hook: string) {
  // #7573 disable dep collection when invoking lifecycle hooks
  pushTarget()
  const handlers = vm.$options[hook]
  const info = `${hook} hook`
  if (handlers) {
    for (let i = 0, j = handlers.length; i < j; i++) {
      invokeWithErrorHandling(handlers[i], vm, null, vm, info)
    }
  }
  if (vm._hasHookEvent) {
    vm.$emit('hook:' + hook)
  }
  popTarget()
}
```
- 可以看到上面**vm.$emit('hook:' + hook)**会派发出一个以**hook:xx**开头的生命周期事件，所以我们可以在组件的外部通过以下事件来监听组件内部的生命周期函数
	- **@hook:created**
    - **@hook:mounted**
    
```js
const hookRE = /^hook:/
Vue.prototype.$on = function (event: string | Array<string>, fn: Function): Component {
  const vm: Component = this
  if (Array.isArray(event)) {
    for (let i = 0, l = event.length; i < l; i++) {
      vm.$on(event[i], fn)
    }
  } else {
    (vm._events[event] || (vm._events[event] = [])).push(fn)
    // optimize hook:event cost by using a boolean flag marked at registration
    // instead of a hash lookup
    if (hookRE.test(event)) {
      vm._hasHookEvent = true
    }
  }
  return vm
}
```
- 最后来看下**_hasHookEvent**这个参数是如何来判断**true/false**的，可以看到源码是通过匹配正则**/^hook:/**，只要以**hook:**开头的事件，就会被检测为**hookEvent**

综上所述，如果你想给一个Vue组件添加生命周期函数有3个办法：
- 在**vue**组件中自行添加，比如自定义一个事件
- 在模板中通过**@hook:created**的方式来添加
- 在**vue**组件的created中，使用`vm.$on('hooks:created', cb)`或者 `vm.$once('hooks:created', cb)`

## 5. vue+nginx多页面配置
- [vue.config.js(链接)](https://cli.vuejs.org/zh/config/#pages)
```js
// vue.config.js 
module.exports = {
	...
    pages: {
      client: {
        // page 的入口
        entry: 'src/pages/client/main.js',
        // 模板来源
        template: 'public/client.html',
        // 在 dist/index.html 的输出
        filename: 'client.html',
        // 当使用 title 选项时，
        // template 中的 title 标签需要是 <title><%= htmlWebpackPlugin.options.title %></title>
        title: 'Client',
        // 在这个页面中包含的块，默认情况下会包含
        // 提取出来的通用 chunk 和 vendor chunk。
        chunks: ['chunk-vendors', 'chunk-common', 'client']
      },
      aunt: {
        entry: 'src/pages/aunt/main.js',
        template: 'public/aunt.html',
        filename: 'aunt.html',
        title: 'Aunt',
        chunks: ['chunk-vendors', 'chunk-common', 'aunt']
      }
    },
    devServer: {
      historyApiFallback: {
        rewrites: [
          { from: /\/client/, to: '/client.html' },
          { from: /\/aunt/, to: '/aunt.html' }
        ]
      }
    }
}
```

- **router.js**
```js
// router.js
export default new VueRouter({
	routes,
	base: '/aunt/',
	mode: 'history'
})
```

- **nginx**
```nginx
location / {
    root   /data/mservice/projects_fe/resource_manager/dist;
    index  index.html;
}
location /aunt {
    root   /data/mservice/projects_fe/resource_manager/dist;
    try_files $uri /aunt.html;
}
location /client {
    root   /data/mservice/projects_fe/resource_manager/dist;
    try_files $uri /client.html;
}
```

## 参考 

- [手摸手，带你用vue撸后台](https://juejin.im/post/59097cd7a22b9d0065fb61d2)
- [一个合格的中级前端工程师应该掌握的 20 个 Vue 技巧
](https://juejin.im/post/6872128694639394830)
- [Vue钩子函数之钩子事件hookEvent](https://www.tensweets.com/article/5e032df8362e5434baf63394)