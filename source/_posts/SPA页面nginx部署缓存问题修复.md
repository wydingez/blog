title: SPA页面Nginx部署缓存问题修复
author: wyding
tags:
  - nginx
  - vue
categories:
  - 学习笔记
cover: http://wyd-mages.oss-cn-shanghai.aliyuncs.com/5ab90e21f767b41be05690cf864f68c5.jpg
date: 2019-08-09 14:33:00
---
> vue打包部署到服务器上后，即使打包的时候开启了hash来避免缓存，但仍然会有问题，排除后是index.html有缓存，因此需要对nginx做以下配置
<!--more--> 

[参考链接](https://medium.com/@pratheekhegde/setting-caching-headers-for-a-spa-in-nginx-eb2f75f52441)
```js
location /app1{
    alias /home/ubuntu/app1/;
    try_files $uri $uri/ /index.html;
    add_header Cache-Control "no-store, no-cache, must-revalidate";
}
```