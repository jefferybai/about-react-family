# About-programming
## 知识点

- [redux-thunk源码解析](#redux-thunk源码解析)
- [applyMiddleware](#redux-thunk源码解析)
- [高阶组件](#高阶组件)



## redux-thunk源码解析 

作用：redux-thunk是redux的一个中间件，dispatch()接受的参数是一个object， 有了这个中间件disatch可以接受一个function作为参数

```js
function createThunkMiddleware(extraArgument) {
  return ({ dispatch, getState }) => next => action => {
    if (typeof action === 'function') {
      return action(dispatch, getState, extraArgument);
    }

    return next(action);
  };
}

const thunk = createThunkMiddleware();
thunk.withExtraArgument = createThunkMiddleware;

export default thunk;
```

## applyMiddleware
```js
export default function applyMiddleware(...middlewares) {
  return (createStore) => (reducer, preloadedState, enhancer) => {
    // 生成一个store 
    const store = createStore(reducer, preloadedState, enhancer)
    let dispatch = store.dispatch
    let chain = []
    //简陋版的store，里面包含了`getState`和`dispatch`两个方法。
    const middlewareAPI = {
      getState: store.getState,
      dispatch: (action) => dispatch(action)
    }
    //将middlewareAPI作为参数注入到每个middleware中去，执行middleware, 返回一个新的链。
    //中间件函数(store)=>next=>action.
    // chain  [next=>action,...];
    //这个 next 其实store.dispatch.   而`action`就是`dispatch`的action
    chain = middlewares.map(middleware => middleware(middlewareAPI))

    //我们假设有三个中间件,fn1,fn2,fn3,那么下面代码等同于  dispatch=fn1(fn2(fn3(store.dispatch)));
    //可以发现，中间件所组成的dispatch 其实就是一个执行过fn1,fn2,fn3的函数。
    //所以，每个中间件在遇到不是自己处理范围之内的action的时候，会使用next(action)，将其传递给下一个中间件。
    dispatch = compose(...chain)(store.dispatch)

    return {
      ...store,
      dispatch
    }
  }
}
```


## 高阶组件
高阶组件就是一个函数，且该函数接受一个组件作为参数，并返回一个新的组件class。

对比组件中将props属性转变成UI，高阶组件则是将一个组件转换成另一个新组件class。

高阶组件在React第三方库中很常见，比如Redux的connect方法和Relay的createContainer.

高阶组件的用途主要有：

1， 属性代理
2， 抽象state
3， 反向继承
4， 渲染劫持





