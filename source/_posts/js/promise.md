---
title: promise
date: 2019-03-26 22:11:41
tags: [ES6,Promise]
---
# Promise 异步应用
## Promise 例子
Promise 对象用于一个异步操作的最终完成（或失败）及其结果值的表示。


```js
function myPromise(value, time, status) { 
    return new Promise((resolve, reject) => {
        setTimeout(() => {                     //用
            console.log(value);
            if (status) {
                resolve();
            } else {
                reject(value);
            }
        }, time)
    })
}
myPromise(1, 500, true).then(() => {
    return 1;
}).then(() => {
    return myPromise(3, 1500, true);
}).then(() => {
    return myPromise(4, 1500, false);
}).catch((err) => {
    console.log(err);
})
```
## Promise原型

### 方法
#### Promise.prototype.catch(onRejected)
> 添加一个否定(rejection) 回调到当前 promise, 返回一个新的promise。如果这个回调被调用，新 promise 将以它的返回值来resolve，否则如果当前promise 进入fulfilled状态，则以当前promise的肯定结果作为新promise的肯定结果.
#### Promise.prototype.then(onFulfilled, onRejected)
> 添加肯定和否定回调到当前 promise, 返回一个新的 promise, 将以回调的返回值 来resolve.


## 浏览器兼容性
### Desktop
Feature | Chrome | Edge | Firefox | Internet Explorer | Opera | Safari
---|---|---|---|---|---|--- 
Basic Support | 32.0 | (Yes) | 29.0 | No | 19 | 7.1

#### [MDN快速链接-Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)

