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
# redux 源码解析

## createStore 源码解析

`createStore`
```js
// reducer: 响应 actions 返回新的 state
// preloadedState: 初始state
// enhancer: 利用 applyMiddleware 对store 增强
createStore(reducer, preloadedState, enhancer)

// createStore 执行时 初始化一次state
dispatch({ type: ActionTypes.INIT })
//`createStore的返回值`
return {
    dispatch,
    /**
     * 触发一个action，
     * 执行 currentState = currentReducer(currentState, action)，更新state
     * 执行所有的listeners
     */
    subscribe, // 添加监听，状态变化时执行所有的listeners
    getState, // return currentState
    replaceReducer, // 替换成新的reducer
    [$$observable]: observable // 定义一个观察者对象，须有next 接口方法
}
```
## combineReducers 源码解析

```js
export default function combineReducers(reducers) {
  const reducerKeys = Object.keys(reducers) // 对所有的reducers key 进行收集
  ...

  return function combination(state = {}, action) {


    let hasChanged = false
    const nextState = {}

    for (let i = 0; i < finalReducerKeys.length; i++) {
      const key = finalReducerKeys[i]
      const reducer = finalReducers[key]
      const previousStateForKey = state[key]

      // reducer的key 指向的 单独的state
      const nextStateForKey = reducer(previousStateForKey, action)

      // 以reducer的key 存储 每一个reducer 维护的state
      nextState[key] = nextStateForKey 

      hasChanged = hasChanged || nextStateForKey !== previousStateForKey
    }
    return hasChanged ? nextState : state
  }
}
```

## applyMiddleware 源码解析
中间件 就是将 store的dispatch进行增强,使得可以接受一个函数，promise对象等，例如 `redux-thunk` 、`redux-promise`
```js
export default function applyMiddleware(...middlewares) {
  return createStore => (...args) => {
    const store = createStore(...args)

    //提供了 store 的getState 和 dispath 的方法
    const middlewareAPI = {
      getState: store.getState,
      dispatch: (...args) => dispatch(...args)
    }

    const chain = middlewares.map(middleware => middleware(middlewareAPI))
    // 提供一个增强的dispatch方法
    dispatch = compose(...chain)(store.dispatch)

    return {
      ...store,
      dispatch
    }
  }

```


# react-redux 源码简单分析

## Provider
```jsx
class Provider extends Component {
  constructor(props) {
    super(props)
    ...

    this.state = {
      store, // redux 创建的 store 
      // provider 提供的一个 可添加订阅的 方法
      // subscription 本身对 store.subscribe 监听 
      subscription 
    }
  }
  render(){
    return <Context.Provider value={this.state}>
      {this.props.children}
    </Context.Provider>
  }
}

```

## Connect 
```jsx
export default function connectAdvanced(selectorFactory, {...}){

  ...

  const checkForUpdates = ()=>{
    ...
  // If the child props haven't changed, nothing to do here - cascade the subscription update
    // props 不变 
    if (newChildProps === lastChildProps.current) {
      if (!renderIsScheduled.current) {
        notifyNestedSubs()
      }
    } else {
      // Save references to the new child props.  Note that we track the "child props from store update"
      // as a ref instead of a useState/useReducer because we need a way to determine if that value has
      // been processed.  If this went into useState/useReducer, we couldn't clear out the value without
      // forcing another re-render, which we don't want.
      lastChildProps.current = newChildProps
      childPropsFromStoreUpdate.current = newChildProps
      renderIsScheduled.current = true

      // If the child props _did_ change (or we caught an error), this wrapper component needs to re-render
      // 如果 props 改变 组件重新render
      forceComponentUpdateDispatch({
        type: 'STORE_UPDATED',
        payload: {
          latestStoreState,
          error
        }
      })
    }
    ...
  }
  // Actually subscribe to the nearest connected ancestor (or store)
  // didStoreComeFromProps ? null : contextValue.subscription context 提供的 subscription
  // 
  subscription.onStateChange = checkForUpdates
  // 添加订阅
  subscription.trySubscribe()

  // Pull data from the store after first render in case the store has
  // changed since we began.
  // 
  checkForUpdates()


  
}


```

