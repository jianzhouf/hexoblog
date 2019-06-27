---
title: reacthook
date: 2019-06-27 15:28:21
tags:
---

# react Hooks 初探

## useState

```js
const [state, setState] = useState(initialState);
```
`state`的初始值可通过第一个参数`initialState`赋值
数组的解构得到`state`和更新函数`setState`

## useEffect

```js
// didUpdate更新回调，deps 监听变量数组
useEffect(didUpdate, deps)

useEffect(()=>{
    console.log("componentDidMount");
    return ()=>{
        console.log("componentWillUnmount");
    }
})

useEffect(()=>{
    console.log("componentDidUpdate");
},[state])

```


## useMemo/useCallback
用作性能优化

```js

```

