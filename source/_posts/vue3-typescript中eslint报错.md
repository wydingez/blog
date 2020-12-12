title: vue3+typescript中eslint报错
author: wyding
date: 2020-11-18 15:10:02
cover:
  http://cdn.wyd94.top/3dbcafa8-703f-79fd-4bae-c436a01d269b.png
tags:
---
> 最近在学习vue3+typescript中，vue.config.js中出现eslint报错，记录一下解决方案

<!-- more -->
### 1.`'path' should be listed in the project's dependencies. Run 'npm i -S path' to add it`

**.eslintrc.js** [参考链接](https://github.com/benmosher/eslint-plugin-import/issues/1396#issuecomment-511007063)
```js
  settings: { 
    'import/resolver': {
      node: {},
      webpack: { 
        config: require.resolve('@vue/cli-service/webpack.config.js') 
      }
    }
  },
```

### 2.`Require statement not part of import statement`

**.eslintrc.js**
```js
  rules: {
    ...
    '@typescript-eslint/no-var-requires': 0
  }
```