# 扩展redux（中间件和storeEnhancer）：

### 拓展redux主要有两种方法：

- 中间件：实际上是用来增强Redux Store中的dispacth方法
- store Enhancer:更深层次的定制



### 1.1 中间件特点：

- 中间件是独立的函数：是指中间件之间没有依赖关系，独立被一个Redux Store使用，完成一个特定的功能。
- 中间件可以组合使用：类似乐高玩具🥁一样，组合拼装
- 中间件有一个统一的接口：每个中间件实现逻辑不一样，只有遵守统一的接口才能够和Redux和其他的中间件建立联系。



redux中实际的流程是：

- 通过store的dispatch派发action函数
- 经历redux中间件的管道，每个中间件都会对action对象进行处理，如果某个中间件觉得没有必要处理这个aciton对象了就不会吧action对象交给下一个中间件，对这个action对象的处理也就终止了，也就轮不到reducer上场了。
- 然后进入reducer中



🧩中间件构成：

```javascript
//一个什么都不做的中间件代码：
function doNothingMiddleware({dispatch,getState}){
  return function(next){
    return function(action){
      return next(action)
    }
  }
}
```

- 参数： doNothingMiddleware函数接收一个对象作为参数--disptach和getState；这两个参数分别对应redux store上的两个同名函数（不是所有中间件都用的上这两个参数）
- 返回：返回的是一个函数；这个被返回的函数接收一个next类型的参数，这个next是一个函数
  - 如果调用了这个next函数，表示这个中间件完成了自己的功能，把控制权交给下一个中间件

- 接收next为参数的函数的返回：这个函数是以action为参数的函数，他是处理action对象的函数，因为闭包的关系，可以根据需要做很多事情：
  - 调用dispatch派发一个新的action对象
  - 调用getState获得当前的ReduxStore上的状态
  - 调用next告诉Redux当前中间件工作完成了，让Redux调用下一个中间件
  - 访问action对象action上的所有数据

之所以使用了这么多层函数是因为Redux是根据函数式编程的思想来设计的。



### 1.2 使用中间件：

<u>这里直接介绍第二种方法：把applyMiddleware的结果当做store Enhancer ，和其他的Enhancer混合后作为cretateSotre参数传入。</u>

```javascript
//以同时使用redux-thunk和redux devtools增强器为例 

import {createStore ,applyMiddleware,compose} from "redux";
import thunkMiddleware from "redux-thunk";

const win=window;
const storeEnhancers=compose(
	applyMiddleware(...middlewares),
	(win && win.devToolsExtension) ? win.devToolsExtenson() : f =>f
);

const store=createStore(reducer,storeEnhancers);
```

- Redux提供了一个compose函数，可以把多个Store Enhancer函数串接起来成为一个函数。



### 1.3 Promise中间件：

需要清楚的一个事情是redux-thunk中间件是对action的类型进行判断去处理异步请求的。

- action类型是函数类型
- 不是函数类型直接调用next交出控制权让redux调用下一个中间件

了解的小例子🎪：

```javascript
function isPromise(obj) {
  return obj && typeof obj.then === 'function';
}

export default function promiseMiddleware({dispatch}) {
  return (next) => (action) => {
    const {types, promise, ...rest} = action;
    if (!isPromise(promise) || !(action.types && action.types.length === 3)) {
      return next(action);
    }

    const [PENDING, DONE, FAIL] = types;

    dispatch({...rest, type: PENDING});
    return action.promise.then(
      (result) => dispatch({...rest, result, type: DONE}),
      (error) => dispatch({...rest, error, type: FAIL})
    );
  };
}
```



### 2 Store Enhancer：

### 2.1 增强器接口

🎰 Redux提供的创建Store的函数叫做CreateStore；这个函数是可以接收三个参数的：

- reducer
- initalState初始状态
- StoreEnhancer函数



🧩一个什么都不做的StoreEnhancer是这个样子滴：

```javascript
// doNothingEnhancer函数
const doNothingEnhancer=(createStore)=>(reducer,initalState,enhancer)=>{
  const store=createStore(reducer,initalState,enhancer);
  return store;
}
```

- 基本套路就是把createStore传递给函数，
- 利用传递给createStore函数的参数去创建一个新的stroe
- 返回这个新的store



一个Store对象中包含如下接口：(实现一个store Enhancer,💎功夫全在于如何“制造出定制款”的store对象)

- dispatch
- subscribe
- getState
- replaceReducer



🌰想要增强器给每个dispatch函数的调用都输出一个日志，那么就实现一个logEnhancer,代码如下：

```javascript

const logEnhancer=(createStore) => (reducer,initalState,enhancer) => {
  const store = createStore(reducer,initalState,enhancer);
  
  const originalDispatch=store.dispatch;           //保存store函数上dispatch的引用
  store.dispatch=(action)=>{
    console.log("dispatch action :",action);
    originalDispatch(action);
  };
  return store;
}
```

宗旨：保本增新💡

- 将store上的某个函数引用存下来
- 给这个函数一个新的实现
- 完成增强功能后，调用原有的函数，保持原有的功能





