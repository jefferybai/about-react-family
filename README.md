# About-programming
## 知识点

- [redux-thunk源码解析](#redux-thunk源码解析)


## redux-thunk源码解析 

作用：redux-thunk是redux的一个中间件，dispatch()接受的参数是一个对象， 有了这个中间件disatch可以接受一个函数作为参数

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


