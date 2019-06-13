---
title: ios兼容问题
date: 2019-05-29 17:16:07
tags:
---

# ios 混合webview 兼容问题

## ios键盘收起导致页面上移
产生原因： 为了用户体验，`input`聚焦时，使得`body`的`scrollTop`加上了键盘的高度，导致页面上移；

```js
if (device.ios()) {
  let timer
  window.addEventListener("focusin", (e) => {
    if (timer) {
      clearTimeout(timer)
    }
  })
  window.addEventListener("focusout", (e) => {
    timer = setTimeout(() => {
      e.target.scrollIntoView()
    }, 200)
  })
}
```