# 关于redux-thunk，redux-saga，以及redux-observable的一些理解

### 1 redux-thunk实现todolist：

```javascript
//index.js(store文件夹中)
//使用中间件的官方配置

import {createStore,applyMiddleware,compose} from "redux"; //compose函数，可以把多个Store Enhancer函数串接起来成为一个函数
import reducer from "./reducer";
import thunk from "redux-thunk";

const composeEnhancers =
  typeof window === 'object' &&
  window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?   
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({
    }) : compose;

const enhancer = composeEnhancers(
        applyMiddleware(thunk),
      );
      const store = createStore(reducer, enhancer);

export default store;

//actionCreator.js
import  {CHANGE_INPUT_VALUE,ADD_TODO_ITEM,HANDLE_DELETE_ITEM,GET_INIT_STATE} from "./actionTypes";
import axios from "axios";
export const getInitStateAction=(data)=>{
    return{
        type:GET_INIT_STATE,
        data
    }
}
export const getDataAction=()=>{
    return (dispatch)=>{
        axios.get("./api/list.json").then((res)=>{
            const data=res.data;
            const action=getInitStateAction(data);      //拿到数据后要派发aciton同步
            dispatch(action);
        })
    }
}

//todolist.jsx中的代码

import React, { Component } from 'react';
import "./style.css";
import 'antd/dist/antd.css';
import store from "./store";
import * as actions from "./store/actionCreator";
import TodoListUi from "./todolistUI";
class Todolist extends Component {
    constructor (props){
        super(props);
        this.state=store.getState();
        this.handleInputChange=this.handleInputChange.bind(this);
        this.handleStoreChange=this.handleStoreChange.bind(this);
        this.handleBtnClick=this.handleBtnClick.bind(this);
        this.handleDeleteItem=this.handleDeleteItem.bind(this);
        store.subscribe(this.handleStoreChange);
    }
    //生命周期函数
    componentDidMount(){
        const action=actions.getDataAction();       //🍿此处会执行第1次dispatch
        store.dispatch(action);                     //🍿此处会执行第2次dispatch           
    }
}
export default Todolist;

```

🌟能够发现在使用redux-thunk过程中一共执行了两次dispatch函数：

- 🦁第1次是在todolist.js中，将creator.js中const action=actions.getDataAction()通过dispatch发给store并自动执行getDataAction方法中返回的内部的函数～！！！；

- 🐺这个自动执行的函数自己就可以接收一个dispatch为参数的方法，就是在actioncreator中的dispatch，接收到请求的回包，然后继续派发action改变store内数据。

（正常来说action.js中的action创建函数都是返回对象的，但是actions.getDataAction却是返回函数，就是因为使用了redux-thunk才能让它得以为所欲为😤）



### 2 redux-saga：

```javascript

//store里面的配置项
import {createStore,applyMiddleware,compose} from "redux";
import reducer from "./reducer";
import thunk from "redux-thunk";
import createSagaMiddleware from 'redux-saga';//引入创建saga中间件的方法；
import MySaga from "./sagas";//引入saga文件

const sagaMiddleware=createSagaMiddleware();//创建一个sagamiddleware中间件

const composeEnhancers =
  typeof window === 'object' &&
  window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?   
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({
    }) : compose;

const enhancer = composeEnhancers(
        applyMiddleware(sagaMiddleware),      //通过applymiddleware中间件去使用这个中间件
      );
const store = createStore(reducer, enhancer);
sagaMiddleware.run(MySaga);             //通过sagamiddleware中间件去运行mysaga文件。
export default store;


//Mysaga.js   一定要导出一个generator函数
import {takeEvery,put} from "redux-saga/effects";
import {GET_SAGA} from "./actionTypes";
import * as actions from "./actionCreator";
import axios from "axios";

function* getSaga(){
    console.log("NBA");
    try{
        const res= yield axios.get("./api/list.json");
        const action=actions.getInitStateAction(res.data);
        console.log(action);
        yield put(action);//派发action
    }catch(e){
        console.log("happyending")
    }

};
function* mySaga() {
    yield takeEvery(GET_SAGA, getSaga);//这个回调函数可以是一个generator函数或者普通的函数
    //通过takeEvery去捕获每一次派发出来的action，如果这个actiontype等于GET_SAGA，那么就执行getSaga方法
  }
  export default mySaga;
```

下面说下过程：

- 1.配置完了后，首先通过createSagaMiddleleware去创建一个sagamiddleware； 然后通过applyMiddleware去组装使用这个创建出来的sagamiddleware；
- 2.saga.js里面一定要导出一个generator函数，里面通过使用takeEvery方法抓取指定的actionType后执行相应的方法，最好这个方法也是一个generator函数，从而得到相应的数据；
- 3.最后把取来的数据结果再创建一个action通过put派发给store，store接收到action后又给了reducer，在reducer中进行相应的处理。
- ⭐理解：枪组装好了，要想使用还得装子弹，而我们自己写的saga.js就好比枪里的子弹，枪要想使用还得安子弹🚄