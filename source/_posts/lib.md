---
title: 优秀的三方库收集
date: 2019-05-09
---

## [html2canvas](https://github.com/niklasvh/html2canvas)
- 可做截屏功能
- 可做某个片段／图片的 特效功能，例如 google 灭霸特效

```js
html2canvas(document.body).then(function(canvas) {
    document.body.appendChild(canvas);
});
```


<!--more-->

## [chance](https://github.com/chancejs/chancejs)
- 随机字符串，数字等的极简主义生成器
- 编写自动化测试

```js
chance.string()
chance.integer({ min: -20, max: 20 })
```

## [Lodash](https://lodash.com/)
- Lodash通过处理数组，数字，对象，字符串等麻烦使得JavaScript更容易



