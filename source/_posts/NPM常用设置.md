title: NPM常用设置
author: wyding
tags:
  - npm
categories: []
cover: 
  https://wyd94-1302884909.cos.ap-nanjing.myqcloud.com/cee5ef7b52c9e6e0f16dba2c08ed69a4.jpg
date: 2020-09-11 17:11:00
---
> 收集了npm的一些常用设置

<!-- more -->

## 1. 本地npm仓库搭建
```bash
$ npm I sinopia -g
$ sinopia

// the server will work on localhost:4873
// and then you can publish your package to the server

$ npm set registry http://localhost:4873
$ npm adduser --registry http://localhost:4873

$ npm publish
$ npm unpublished

// to see the versions of package that had been published
$ npm view xxx versions

// install the target of version you want [rollback]
$ npm install -g xxx@version
```

## 2. npm 安装 chromedriver 失败的解决办法
```bash
$ npm install chromedriver --chromedriver_cdnurl=http://cdn.npm.taobao.org/dist/chromedriver

// 或者一劳永逸的方法
$ npm config set chromedriver_cdnurl https://npm.taobao.org/mirrors/chromedriver

// 等价于直接修改npm的配置文件.npmrc
// chromedriver_cdnurl https://npm.taobao.org/mirrors/chromedriver
```

## 3. node-sass安装失败
```bash
npm rebuild node-sass -force

// 或者一劳永逸的方法
npm config set sass_binary_site https://npm.taobao.org/mirrors/node-sass/

// 等价于直接修改npm的配置文件.npmrc
// sass_binary_site=https://npm.taobao.org/mirrors/node-sass/
```