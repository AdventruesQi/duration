### JSPang--react-hook：
### 一、介绍：


* react16.8版本以上会有useState这个东东🦁

```javascript
import React, { useState} from 'react';

function ExampleHook(){
    const [count,setCount]=useState(0);
    return (
        <div>
            <p>你点击了{count}次</p>
            <button onClick={()=>{setCount(count+1)}}>Click</button>
        </div>
    )
}
export default  ExampleHook;
```

### 二、useState方法的介绍：
🤖 useState是一个函数，返回一个数组，换一个是当前的state，一个更新state的函数。useState不能用于条件判断语句中。

* 通过解构赋值对一个变量初始化
* 通过解构赋值对更新state函数进行初始化
* useState这个函数接收的参数是状态的初始值(Initial state)，它返回一个数组。
* setCount函数，这个函数接收的参数是修改过的新状态值
* React自动帮助我们记忆了组件的上一次状态值

```javascript
const [ count , setCount ] = useState(0);

let _useState = userState(0)
let count = _useState[0]
let setCount = _useState[1]
```


### 三、useEffect方法介绍：
useEffect一个函数代表了之前的componentDidMount（组件装载完毕）和ComponentDidUpdate（组件更新完毕）两个生命周期函数。

* 使用useEffect就要清楚对应之前的两个生命周期函数这个知识点。
* useEffect是异步的，并不会阻断我们的视图更新。

### 四、useEffect实现ComponentWillUnmont生命周期函数。
（ComponentWillUnmount就是在组件销毁之前执行的一个动作）;

```javascript
import React, { useEffect,useState} from 'react';
import{BrowserRouter as Router,Route,Link} from "react-router-dom";

function Index(){
    useEffect(()=>{
        console.log("老弟你来了~！");
        return ()=>{
            console.log("老弟你走了啊");
        }
    },[ ])
    return <div>我是首页</div>
}
function List(){
    useEffect(()=>{
        console.log("++++++++++！");
        return ()=>{
            console.log("------------");
        };
    });
    return <div>我是列表页</div>
}


function ExampleEffect(){
    const [count,setCount]=useState(0);
    useEffect(()=>{
        console.log(`useEffect ${count}`);
        return ()=>{
            console.log(`我就是要走的时候需要执行的${count}值。`);
        }
    },[count]);
    return (
        <div>
            <p>点击了多{count}</p>
            <button onClick={()=>{setCount(count+1)}}>点击我</button>
            <Router>
                <ul>
                    <li><Link to="/">首页</Link></li>
                    <li><Link to="/List">列表页</Link></li>
                </ul>
                <Route path="/" exact component={Index}></Route>
                <Route path="/List" component={List}></Route>         
            </Router>

        </div>
    )
}
export default ExampleEffect

```
分析下上面的index组件，这里面的useEffect函数接收的那个参数函数内部返回了一个函数，是用于副作用的解绑的，但是会存在一个问题，只要状态发生变化，他就会执行一次（没有传入第二个参数[ ]的时候)；

* [ ] :里面是空的，我们不使用Index的时候，返回的函数才执行。
* [count] :在ExanpleEffect中的[ ]里面传入组件的状态值参数的时候，意思就是，只要count状态发生变化，我们就执行那个return返回来的函数。

### 五、useContext父子组件传值
引入：之前我们使用class声明组件的时候使用的都是props组件来进行组件建的传值的。但是现在使用function的形式声明组件的时候，就得有解决组件间传值的一个解决办法，于是就引入了useContext方法来解决此问题。

* useReducer是用来解决状态共享的问题的。
* useContext是用来解决组件间传值的问题的。

例子🌰：

```javascript
import React, { useState,useContext,createContext} from 'react';
const Ct=createContext();
function ExamplegetContext(){
    let getValue=useContext(Ct);
    return (
    <h3>{getValue}</h3>
    )
}

function ExampleUseContext(){

    const [count,setCount]=useState(0);
    return (
        <div>
           <p>这个讲的值组件间的传值:{count}</p>
            <button onClick={()=>{setCount(count+1)}}>Click</button>
            <Ct.Provider value={count}>
                <ExamplegetContext/>
            </Ct.Provider>
        </div>
    );
}
export default ExampleUseContext;
```
使用步骤：

*  🌽想要分享值的组件中需要引入createContext方法，然后通过这个方法去创建一个"context"；const Ct=createContext(); 
*  🥕 将创建好的Ct通过Ct.Provider的组件形式放到组件中去，然后添加value属性，将想要分享的属性放到value中去。
*   🥐 子组件想要获取到从父组件传递过来的值，同样的，需要引入一个useContext的方法，然后在子组件里定义一个变量，通过这个变量去接收通过useContext(Ct)方法从父组件里接收到的值。最后放到组件中使用即可。

### 六、useReducer的介绍和简单的使用
通过useReudcer和useContext的组合使用从而达到一个类似Redux的效果;

```javascript
import React, {useReducer} from 'react';

function ExampleUseReducer() {
    const [count,dispatch] = useReducer((state, action) => {
        switch (action) {
            case "add":
                return state + 1
            case "sub":
                return state - 1
            default:
                return state
        };
    }, 0);
    return (
        <div>
            <p>现在的分数是{count}</p>
            <button onClick={()=>{dispatch("add")}}>+</button>
            <button onClick={()=>{dispatch("sub")}}>-</button>
        </div>
    )
};
export default ExampleUseReducer;
```

* 引入useReducer方法，然后....就感觉和reducer很像。它接收两个参数，第一个是一个接收两个参数的函数(state,aciton);第二个是初始化的值。
* 主要是看在函数加减的调用那部分，onClick={()=>{dispatch("add")}}，通过onClick的箭头函数中的dispatch直接派发了action，从而改变状态值，是真的很6。


### 七、useReducer代替reducer小案例（1）
引入：使用useReducer和useContext的使用，从而达到redux的使用。
简单的说下本节的大概流程：

* 写好各个组件，然后拼在一起。无可避免的需要使用"context" ；
* 通过createContext组件来创建context进行分享的时候，也需要单独的创建一个组件去对"想要获得它的值"的组件进行包裹

下面这个是Color组件：

```javascript
import React, { createContext} from 'react';
export const colorContext=createContext({});
export const Color = props=>{
       return (
           <colorContext.Provider value={{color:"blue"}}>
               {props.children}
           </colorContext.Provider>
       )
} 
```
* 传入的props用于渲染子组件的
* 在子组件那需要使用useContext进行接收那个创建的context。

### 八 useReducer代替reducer小案例（2）
🍱 怎么说呢，还是直接上代码吧

```javascript
//总的渲染的大的组件
import React from 'react';
import ShowText from './ShowText';
import Buttons from './Buttons';
import { Color } from './Color';

function Case(){
    return (
        <div>
            <Color>
                <ShowText/>
                <Buttons></Buttons>
            </Color>
        </div>
    )
}
export default Case;

//接着是展示文字的ShowText组件
import React ,{useContext}from 'react';
import { ColorContext } from './Color';

function ShowText(){
    const {color}=useContext(ColorContext);
    return (
        <div style={{color:color}}>文字的颜色</div>
    )
}
export default ShowText;


//然后是改变文字颜色的按钮组件Buttons
import React,{useContext} from 'react';
import {  UPDATE_COLOR, ColorContext } from './Color';


function Buttons(){
    const {dispatch}=useContext(ColorContext)
    return (
        <div>
            <button onClick={()=>{dispatch({type:UPDATE_COLOR,color:"red"})}}>红色</button>
            <button onClick={()=>{dispatch({type:UPDATE_COLOR,color:"blue"})}}>蓝色</button>
            <button onClick={()=>{dispatch({type:UPDATE_COLOR,color:"orange"})}}>橙色</button>
        </div>
    )
}
export default Buttons;


//最后是对这些组件进行包裹的color分享组件
import React, { createContext,useReducer} from 'react';

export const ColorContext=createContext({});

export const UPDATE_COLOR="UPDATE_COLOR";

const reducer=(state,action)=>{
    switch(action.type){
        case "UPDATE_COLOR":
            return action.color
        default :
        return  state
    }
}

export const Color = props=>{
    //useReducer的参数是需要传递一个reducer函数进去，以及初始化的状态值。 "pink"就是color的初始化状态值。
    const [color,dispatch] = useReducer(reducer,"pink")
       return (
           <ColorContext.Provider value={{color,dispatch}}>
               {props.children}
           </ColorContext.Provider>
       )
} 
```

* 大体上的实现就是通过useContext去将color还有dispatch传递给子组件。
* 需要知道useReducer的函数返回的是一个当前的state以及dispatch方法。


### 九、useMemo解决组件重复渲染的问题

引入：useMemo主要用来解决使用React hooks产生的无用渲染的性能问题。使用function的形式来声明组件，失去了shouldCompnentUpdate（在组件更新之前）这个生命周期，也就是说我们没有办法通过组件更新前条件来决定组件是否更新。而且在函数组件中，也不再区分mount和update两个状态，这意味着函数组件的每一次调用都会执行内部的所有逻辑，就带来了非常大的性能损耗。useMemo和useCallback都是解决上述性能问题的。

```javascript
import React, {useMemo,useState} from 'react';


function ExampleUseMemo(){
    const [xiaohong,setXiaohong]=useState("搓澡Hong在待客状态-----，");
    const [xiaogang,setXiaogang]=useState("按摩刚在待客状态ing");

    return (
        <div>
            <button onClick={()=>{setXiaohong(new Date().getTime()+"红")}}>我是搓澡红</button>
            <button onClick={()=>{setXiaogang(new Date().getTime()+"刚")}}>我是按摩刚</button>
            <Childcomponent name={xiaohong}>{xiaogang}</Childcomponent>
        </div>
    )
}

function Childcomponent({name,children}){
    function changeHong(){
        // console.log("他来了||ヽ(*￣▽￣*)ノミ|Ю，搓澡红来了");
        console.log(name);
        console.log(children);
        return "小红向我们走来了," + name
    }
    //useMemo需要出入一个传入一个函数，第二个参数是name的意思是，当name发生变化的时候才执行前面的那个函数。
    //效果：当小红发生变化的时候，才会执行重新渲染chagneHong的函数，点击按钮小刚的时候，不会执行changeHong函数。
    const actionHong=useMemo(()=>{changeHong(name)},[name]);
    // const actionHong=changeHong(name);
    return (
        <div>
            <div>{actionHong}</div>
            <div>{children}</div>
        </div>
    )
}
export default ExampleUseMemo;
```

### 十、useRef获取dom和保存变量
```javascript
import React, { useState,useRef,useEffect} from 'react';

function ExampleUseRef(){
   //获取dom
    const inputEl=useRef(null);
    const changeIt=()=>{
        inputEl.current.value="hello world";
        console.log(inputEl);
    };

    const [curtext,setCurtext]=useState("这个是耿鬼么？");
    //useRef保存变量的值
    const curT=useRef();
    useEffect(()=>{
       curT.current=curtext;
       console.log("curT",curT.current);
    });

    return (
        <div>
            <input ref={inputEl} type="text" />
            <button onClick={changeIt}>进化吧~！</button>
            <br/>
            <input onChange={(e)=>{setCurtext(e.target.value)}}  value={curtext} />
        </div>
    )
}
export default ExampleUseRef;
```
* 还是主要看怎么用吧：
* 一个是获取元素的场景
* 一个是结合useEffect来保存变量的情况

### 十一、自定义组件hooks
* 自定义hooks函数默认use开头

```javascript
import React, { useState ,useEffect ,useCallback } from 'react';

function useWinSize(){
    const [ size , setSize] = useState({
        width:document.documentElement.clientWidth,
        height:document.documentElement.clientHeight
    })

    const onResize = useCallback(()=>{
        setSize({
            width: document.documentElement.clientWidth,
            height: document.documentElement.clientHeight
        })
    },[]) 
    useEffect(()=>{
        window.addEventListener('resize',onResize)
        return ()=>{
            window.removeEventListener('resize',onResize)
        }
    },[])

    return size;

}
function MyHooks(){

    const size = useWinSize()
    return (
        <div>页面Size:{size.width}x{size.height}</div>
    )
}

export default MyHooks 
```






