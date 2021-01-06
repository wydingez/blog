title: 企业微信js-sdk集成
author: wyding
cover: 'https://cdn.wyd94.top/3013fcc1f26003e066406adf30bc29d2.jpeg'
date: 2021-01-06 14:57:07
tags:
---
> 最近公司企业微信项目集成js-sdk，记录使用流程,[参考网址](https://work.weixin.qq.com/api/doc/90001/90144/90547)

<!-- more -->
## 1.安装模块weixin-js-sdk
```bash
npm i weixin-js-sdk --save
```

## 2.通过corpid和corpsecret获取token
获取access_token  https://work.weixin.qq.com/api/doc/90000/90135/91039
```js
const { data } = await request({
  url: '/wx-server/cgi-bin/gettoken',
  method: 'get',
  params: {
    corpid: this.corpid,
    corpsecret: this.corpsecret
  }
})

if (data.errcode === 0) return data.access_token
return Promise.reject('Get Token error')
```

## 3.通过token获取ticket
获取企业的jsapi_ticket  https://work.weixin.qq.com/api/doc/90000/90136/90506
```js
const { data } = await request({
  url: '/wx-server/cgi-bin/get_jsapi_ticket',
  method: 'get',
  params: {
    access_token: this.token
  }
})

if (data.errcode === 0) return data.ticket
return Promise.reject('Get Jsapi ticket error')
```

## 4.生成签名
```js
const signature = sha1(`jsapi_ticket=${this.ticket}&noncestr=${this.noncestr}&timestamp=${this.timestamp}&url=${window.location.href.split('#')[0]}`)
```

## 5.初始化设置
```js
this.wx.config({
  beta: true, // 必须这么写，否则wx.invoke调用形式的jsapi会有问题
  debug: true, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
  appId: this.corpid, // 必填，企业微信的corpID
  timestamp: this.timestamp, // 必填，生成签名的时间戳
  nonceStr: this.noncestr, // 必填，生成签名的随机串
  signature: this.generateSignature(), // 必填，签名，见 附录-JS-SDK使用权限签名算法
  jsApiList: this.jsApiList // 必填，需要使用的JS接口列表，凡是要调用的接口都需要传进来
})
```

在成功后的回调函数中调执行其它的api操作
```js
this.wx.ready(function() {
  // config信息验证后会执行ready方法，所有接口调用都必须在config接口获得结果之后，config是一个客户端的异步操作，所以如果需要在页面加载时就调用相关接口，则须把相关接口放在ready函数中调用来确保正确执行。对于用户触发时才调用的接口，则可以直接调用，不需要放在ready函数中。
  console.log('[wx init success]')
  succesCb()
})
```

## 6.注意一下几点
* 以上只可在企业微信端调试
* **jsApiList**枚举了可以调用的wx的api接口
* 生成签名时，需要使用的url必须形为以下结构:**http://xxx.yyy.com/**，注意最后面需要带上**/**
* 可信域名需要加上当前项目的域名
* [企业微信错误码查询工具](https://open.work.weixin.qq.com/devtool/query?e=40093)
* [企业微信签名测试工具](https://work.weixin.qq.com/api/jsapisign)