title: JS 接口返回的文件流如何下载
author: wyding
tags:
  - js
categories: []
date: 2020-09-11 16:49:00
---
> 工作中，一般的文件下载我们可以通过**window.open(/xxxx/downloadFileGet?id=xxx)**的方式，这种属于**get**请求，但是如果我们的使用**post**请求，该如何来实现呢？🤔

<!-- more -->
以[axios](http://www.axios-js.com/zh-cn/docs/)为例，记录下代码中的实现
```javascript
axios.post(
	'/xxxx/downloadPost',
    {...params},
    {
    	responseType: 'blob'
    }
).then((res) => {
  if (!!window.ActiveXObject || 'ActiveXObject' in window) {
    // 兼容IE
    var blob = new Blob([res.data], { type: 'application/json;charset=UTF-8' })
    window.navigator.msSaveBlob(blob, fileName)
  } else {
    let reader = new FileReader()
    reader.readAsDataURL(res.data, 'UTF-8')
    reader.onload = function (evt) {
      let a = document.createElement('a')
      a.href = evt.target.result
      a.download = fileName
      document.body.appendChild(a)
      a.click()
      document.body.removeChild(a)
    }
  }
})
```

## 参考

- [JS : Blob() 转换二进制下载文件流实例](https://juejin.im/post/6844903700222181384)