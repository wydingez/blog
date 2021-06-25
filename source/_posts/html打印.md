title: html打印
author: wyding
tags:
  - html
  - css
categories: []
date: 2021-06-25 10:52:00
cover:
  https://cdn.wyd94.top/IMG_3413.jpeg
---
> 难免在工作中遇到需要打印的功能，记录常用的一些属性

<!-- more -->

## @media print

### 1. print-color-adjust 
> 此属性用来设置浏览器列印背景颜色的行为
- `economy` [默认值]: 允许浏览器自用调整
- `exact` : 使用你设定好的颜色
```css
@media print {
  * {
    -webkit-print-color-adjust: exact !important;
  }
}
```

### 2. media 换页分割
- [`break-after`](https://developer.mozilla.org/en-US/docs/Web/CSS/break-after): 该元素后面进行强制中断
- [`break-before`](https://developer.mozilla.org/en-US/docs/Web/CSS/break-before): 该元素前面面进行强制中断
- [`break-inside`](https://developer.mozilla.org/en-US/docs/Web/CSS/break-inside): 属性用于设置是否在指定元素中插入分页符

```
@media print{
  .noBreak {
    break-inside: avoid;
  }
}
```

### 3. 打印隐藏元素
```
@media print {
  .noPrint {
  	display: none;
  }
}
```

## @page
> 设置打印纸张属性
- size: A4 / A5, 页面方向（portrait / Landscape）
- margin: 设置 margin 边距

```css
@page {
  size: A4 portrait;
  margin-top: 3cm;
}

@page :first {
  margin-left: 4cm;
}
```

## debug
> Chrome 设置
![p1](https://cdn.wyd94.top/1181624590591_.pic_hd.jpg)
![p2](https://cdn.wyd94.top/1191624590596_.pic_hd.jpg)


## 参考

- [原來前端網頁列印，不是只要 window.print() 就好了](https://medium.com/unalai/%E5%8E%9F%E4%BE%86%E5%89%8D%E7%AB%AF%E7%B6%B2%E9%A0%81%E5%88%97%E5%8D%B0-%E4%B8%8D%E6%98%AF%E5%8F%AA%E8%A6%81-window-print-%E5%B0%B1%E5%A5%BD%E4%BA%86-7af44cacf43e)