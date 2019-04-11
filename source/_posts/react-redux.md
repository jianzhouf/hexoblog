---
title: react-redux
date: 2019-04-11 16:19:22
tags: 

---
> 初探redux，主要记录简单的redux使用方法，理解action、reducer、store的关系；react结合redux，异步简单实例

<!-- more -->


# 关于redux

## 三大原则
1. 单一数据源 
>整个应用的 state 被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 store 中。
2. State 是只读的 
>唯一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象。
3. 使用纯函数来执行修改 
>为了描述 action 如何改变 state tree ，你需要编写 reducers。


创建store

```js
import {createStore} from "redux"
const ADD_TODO = "ADD_TODO"
/*action*/
function addTodo (){
  return {
    type: ADD_TODO,
  }
}
/*reducer*/
function todos(state={index:1}, action){
    switch(action.type){
        case ADD_TODO:
            return  {...state, index: state.index+1}  
        default:
            return state
    }
}
const store = createStore(todos)

store.dispatch(addTodo())
store.getState() // {index:2}
```
