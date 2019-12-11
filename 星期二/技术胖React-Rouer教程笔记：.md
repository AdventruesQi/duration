### 技术胖React-Rouer教程笔记：

* 🖊：安装：

```javascript

```

```javascript
npm install --save react-router-dom
```
快速生成react组件的方法：
* cccs---->生成组件
* BrowserRouter 路由
* Route 线路
* Link 相当于a标签，值改变url

### 路由的动态传值：
🌠 四步走


* 设置规则---Route path="/list/:id" component={List} 
* 传递值---"/:id"
* 接受值--- list组件的cmd生命周期函数中接收这个值。
* 显示内容--- 正常渲染

```javascript
import React, { Component } from 'react';
import { BrowserRouter, Route, Link } from "react-router-dom";
import Home from './pages/Home';
import List from './pages/List';
class AppRouter extends Component {
    constructor(){
        super()
    }
    render() {
        return (
            <BrowserRouter>
                <div>
                    <ul>
                        <li><Link to="/">首页</Link></li>
                        <li><Link to="/list/">列表页</Link></li>
                    </ul>
                    <Route path="/" exact component={Home} />
                    <Route path="/list/:id" component={List} />
                </div>
            </BrowserRouter>
        )
    }
}

export default AppRouter;
```
🏝:这里面主要是说一下过程：Route组件的动态传值是通过"/:id";那么在list组件里面就能接收到一个”浏览器对象“，（在list组件的cmd生命周期中，可以通过this.props看到这个浏览器对象，里面就包含了动态的值）;    

### 重定向：🦖
* 利用标签来跳转    :Redirect to="/Boss/"/
* 利用编程的方式进行跳转: this.props.history.push("/Boss/")




