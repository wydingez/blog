title: vue之keep-alive中，如何缓存同一个页面组件，参数不同
author: wyding
tags:
  - vue
  - keep-alive
categories: []
date: 2020-09-09 15:22:00
---
> 最近项目中使用keep-alive动态缓存不同页面时，遇到如果是同一个页面，但是在参数不同的时候(如`/base/page/code1` 和 `/base/page/code2`)，会出现缓存的bug，研究过程中记录下

<!-- more -->
## 1. 原始keep-alive兼容多标签页的实现方法
主要原理：依赖于`<keep-alive>`标签中的[`include`](https://cn.vuejs.org/v2/api/#keep-alive)属性动态地添加需要缓存的页面组件名称，`<router-view>`上添加`key`属性是为了兼容一个页面，不同的参数而作出的调整
```html
<keep-alive :include="cachedViews">
  <router-view :key="key" />
</keep-alive>
```
```vue
key () {
  return this.$route.fullPath
}
```

## 2. 遇到的问题
问题：由于是同一个页面，所以`include`中缓存的组件名称相同。造成如果同时存在多个一样名称的页面时，此时如果删除其中一个，并不会触发`keep-alive`内部去`$destory`这个组件实例，因为另外一个相同名字的组件此时还存在，后面如果重新打开这个关闭的页面，之前的页面的状态并没有释放，这里可以参考`vue`源码中`keep-alive`的实现
```javascript
// 监听include属性，动态的完成cache的添加和删除
this.$watch('include', val => {
  pruneCache(this, name => matches(val, name))
})
  
function pruneCache (keepAliveInstance: any, filter: Function) {
  const { cache, keys, _vnode } = keepAliveInstance
  for (const key in cache) {
    const cachedNode: ?VNode = cache[key]
    if (cachedNode) {
      const name: ?string = getComponentName(cachedNode.componentOptions)
      if (name && !filter(name)) {
        pruneCacheEntry(cache, key, keys, _vnode)
      }
    }
  }
}

function pruneCacheEntry (
  cache: VNodeCache,
  key: string,
  keys: Array<string>,
  current?: VNode
) {
  const cached = cache[key]
  if (cached && (!current || cached.tag !== current.tag)) {
    cached.componentInstance.$destroy()
  }
  cache[key] = null
  remove(keys, key)
}
```

## 3. 解决思路

### 1. ~~首先想的是，能不能获取并存储keep-alive实例，然后在关闭页面的时候根据url动态的去匹配，需要删除的缓存实例，如果存在，则强制进行$destory，后来看了下这个：`<keep-alive> 是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在组件的父组件链中`，发现根本取不到。。~~
### 2. 查阅了一些文档，发现可以通过如下方式来实现
```javascript
key () {
  // 原理：通过动态缓存和修改router-view中key的属性值
  let marker = window.sessionStorage.getItem(this.$route.fullPath)

  if (!marker) {
    window.sessionStorage.setItem(this.$route.fullPath, new Date().getTime())
    marker = window.sessionStorage.getItem(this.$route.fullPath)
  }
  return this.$route.fullPath + '_' + marker
}
```

## 参考

- [1] [Vue Doc](https://cn.vuejs.org/v2/api/#keep-alive)
- [2] [Vue-element-admin](https://github.com/PanJiaChen/vue-element-admin/issues/1629#issue-413678320)
- [3] [Vue 源码解析](http://caibaojian.com/vue-analysis/extend/keep-alive.html#%E5%86%85%E7%BD%AE%E7%BB%84%E4%BB%B6)