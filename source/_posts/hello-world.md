title: webpack之require.context / require的正确使用方法
tags: []
categories:
  - webpack
date: 2019-08-06 15:37:00
---
> vue中我们有时候会把多个route拆到不同的文件中去，合并的时候我们需要一个一个的去引用，这样不够友好：

因此webpack的require.context和require应运而生，它可以帮助我们动态的去引用目录下的文件：[参考链接](https://webpack.docschina.org/guides/dependency-management/)

## require.context
### 1. 使用方法
```js
// 这样使用静态的目录正则是没问题的
let context = require.context('src/modules', true, /\/(core|demos)\/views\/index.js/)
console.log(context.keys())

```

### 2. 踩坑点,[动态规则会有问题](https://github.com/webpack/webpack/issues/4772#issuecomment-424953990)
```js
// 然而如果使用动态生成的方式生成正则，会有问题
const activeModules = ['core', 'demos'].join('|')
const reg = new RegExp(`\\/(${activeModules})\\/views\\/index.js`)
let context = require.context('src/modules', true, reg)
console.log(context.keys())
```

## require
避免以上问题，就是使用require(variable) and use the ContextReplacementPlugin to provide the "staticness" via config
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