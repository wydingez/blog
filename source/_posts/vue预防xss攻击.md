title: XSS & CSRF
author: wyding
tags:
  - js
categories: []
date: 2021-04-02 17:31:00
cover: 
  https://cdn.wyd94.top/IMG_3345.jpeg
---
> - xss: 跨站脚本攻击（Cross Site Scripting),最常见攻击web网站的方法，攻击这通过注入非法的html标签或者javascript代码，从而控制用户浏览器
- csrf：跨站点请求伪造（Cross-Site Request Forgeries）


<!-- more -->
## XSS

### 主要分为以下三类

- DOM xss
    DOM即文本对象模型，DOM通常代表在html、xhtml和xml中的对象，使用DOM可以允许程序和脚本动态的访问和更新文档的内容、结构和样式。它不需要服务器解析响应的直接参与
    
- 反射型 xss
    反射型XSS也被称为非持久性XSS，是现在最容易出现的一种XSS漏洞。发出请求时，XSS代码出现在URL中，最后输入提交到服务器，服务器解析后在响应内容中出现这段XSS代码，最后浏览器解析执行。
    
- 存储型 xss
    存储型XSS又被称为持久性XSS，它是最危险的一种跨站脚本，相比反射型XSS和DOM型XSS具有更高的隐蔽性，所以危害更大，因为它不需要用户手动触发。 允许用户存储数据的web程序都可能存在存储型XSS漏洞，当攻击者提交一段XSS代码后，被服务器端接收并存储，当所有浏览者访问某个页面时都会被XSS，其中最典型的例子就是留言板。
    
    
### 可能造成的影响

- 利用虚假输入表单骗取用户个人信息
- 利用脚本窃取用户的 Cookie 值，被害者在不知情的情况下，帮助攻击者发送恶意请求
- 显示伪造的文章或图片
    
### 防御

- httpOnly: 在 cookie 中设置 HttpOnly 属性后，js脚本将无法读取到 cookie 信息
- 过滤
    - 输入检查，一般是用于对于输入格式的检查
    - HtmlEncode
    - JavaScriptEncode
        
        
### vue中预防xss
```js
// prevent xss attrak
import sanitizeHtml from './utils/sanitize-html'

Vue.prototype.$sanitize = (html, options = {}, _recursing) => {
    return sanitizeHtml(
      html,
      {
        ...options,
        disallowedTagsMode: 'recursiveEscape'
      },
      _recursing
    )
  }
```

        
## CSRF

### 可能造成的影响

- 利用已通过认证的用户权限更新设定信息等
- 利用已通过认证的用户权限购买商品
- 利用已通过的用户权限在留言板上发表言论
    
### 防御

- 验证码
- 尽量使用 post请求
- Referer check；请求来源限制
- token
    
## 两者的区别

- 通常来说 CSRF 是由 XSS 实现的，CSRF 时常也被称为 XSRF
- 质上讲，XSS 是代码注入问题，CSRF 是 HTTP 问题