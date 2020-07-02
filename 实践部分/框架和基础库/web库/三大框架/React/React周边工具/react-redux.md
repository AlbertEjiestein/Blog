# React-redux

+ react-redux提供了Provider-connect

+ 异步action，需要引入redux-thunk的中间件

  异步action就是对同步action做了一层封装，先请求完数据再dispatch(action)

+ redux中间件 redux-thunk  redux-promise  redux-saga

  redux中间件原理是对dispatch进行了改造，比如异步的action，dispatch的时候先经过中间件对其修改

  ```js
  // logger实现
  let next = store.dispatch
  store.dispatch = function dispatchAndLog(action){
      console.log('dispatching', action)
      next(action)
      console.log('next state', store.getState())
  }
  ```

  

  ![单向数据流](C:/Users/Alber/Pictures/单向数据流.PNG)



基本概念

单项数据流

react-redux

异步action

中间件

