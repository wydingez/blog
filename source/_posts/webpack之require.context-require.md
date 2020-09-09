title: webpack之require.context / require
tags:
  - webpack
  - vue
categories:
  - 学习笔记
cover: >-
  http://wyd-mages.oss-cn-shanghai.aliyuncs.com/c9ece60fccb8eabeacc98e6f712f2db906615b2e.jpg
date: 2019-08-06 15:37:00
---
> `vue`中我们有时候会把多个`route`拆到不同的文件中去，合并的时候我们需要一个一个的去引用，这样不够友好，因此`webpack`的`require.context`和`require`应运而生，它可以帮助我们动态的去引用目录下的文件：[参考链接](https://webpack.docschina.org/guides/dependency-management/)
<!--more--> 

## require.context
### 1. 使用方法
```js
// 这样使用静态的目录正则是没问题的
let context = require.context('src/modules', true, /\/(core|demos)\/views\/index.js/)
console.log(context.keys())

```

### 2. 踩坑点 [动态规则会有问题](https://github.com/webpack/webpack/issues/4772#issuecomment-424953990)
```js
// 然而如果使用动态生成的方式生成正则，会有问题
const activeModules = ['core', 'demos'].join('|')
const reg = new RegExp(`\\/(${activeModules})\\/views\\/index.js`)
let context = require.context('src/modules', true, reg)
console.log(context.keys())
```

## require
避免以上问题，就是使用**require(variable) and use the ContextReplacementPlugin to provide the "staticness" via config**
```js
export default function dynamicImport (type) {
  let ret = []
  const activeModules = JSON.parse(process.env.VUE_APP_ACTIVE_MODULES)
  activeModules.forEach(module => {
    ret.push({
      module,
      data: require(`src/modules/${module}/${type}/index.js`).default
    })
  })
  return ret
}
```