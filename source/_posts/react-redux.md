---
title: react-redux
date: 2019-04-11 16:19:22
tags: 

---

# 关于redux

## 三大原则
1. 单一数据源 
>整个应用的 state 被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 store 中。
2. State 是只读的 
>唯一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象。
3. 使用纯函数来执行修改 
>为了描述 action 如何改变 state tree ，你需要编写 reducers。

<!-- more -->


## 简单的redux 实例

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
store.subscribe(()=>{
    store.getState() // {index:2}
})
store.dispatch(addTodo())

```

## react-redux 异步action 实例
```js
import {createStore,applyMiddleware} from "redux"
import thunkMiddleware from "redux-thunk"
const ADD_TODO = "ADD_TODO"
/*action*/
function addTodo (){
  return {
    type: ADD_TODO,
  }
}
//利用 thunkMiddleware 让 dispach可以识别函数
function asyncAddTodo(){
  return function (dispatch){
    setTimeout(()=>{
      dispatch(addTodo())
    },2000)
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
const store = createStore(todos,applyMiddleware(thunkMiddleware))


//使用react-redux
//入口文件，<Provider> 来 魔法般的 让所有容器组件都可以访问 store，而不必显示地传递它
import {Provider} from "react-redux"
ReactDOM.render(<Provider store={store}><App /></Provider>, document.getElementById('root'));

//利用connect 生成容器组件 代替 store.subscribe， 这个方法做了性能优化来避免很多不必要的重复渲染
import {connect} from "react-redux"
import {asyncAddTodo,addTodo} from "./index"
export default connect((state,ownProps)=>{
return {
 index: state.index
}},(dispatch)=>({
  addTodo: ()=>{
    dispatch(addTodo())
  },
  asyncAddTodo: ()=>{
    dispatch(asyncAddTodo())
  }
}))(App);

```
# createStore 源码解析
# dispatch 源码解析
