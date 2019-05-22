---
title: 移动端遮罩层问题
date: 2019-05-21 17:17:34
tags:
---
# 移动端遮罩层问题
## 移动端 ios safari
<!--more-->
### 方法1 
`body`加上`overflow:hidden`.
PC可以实现 
问题：
在移动端 会有`滚动穿透`问题
定义：移动端弹出fixed弹窗的话，在弹窗上滑动会导致下层的页面跟着滚动，这个叫 “滚动穿透”

### 方法2
`body`加上 `position:fixed`
解决了`滚动穿透`问题
新的问题：
滚动位置丢失！页面会回到顶部！
修复方法：
```js
//以下vue 的代码实现 通过 遮罩层的显示隐藏 切换body 的样式 
 watch: {
    visible: function(val) {
      if (val) {
        this.top =
        document.body.scrollTop || document.documentElement.scrollTop;
        document.body.style.position = "fixed";
        document.body.style.top = -this.top + "px";
      } else {
        document.body.style.position = "";
        document.documentElement.scrollTop = document.body.scrollTop = this.top;
      }
    }
  }
// 注意： 由于fixed 的改变了body的样式 ， 请加上 body{width: 100%}
```