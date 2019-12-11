学习react过程中遇到了this指向的问题，bind方法的理解在bind的那篇文章有提到，也得好好理解，先主要理解下组件的这个过程以及this，文章是摘录的。下面看代码：
```javascript
//假想定义一个ToggleButton开关组件
class ToggleButton extends React.Component{
    constructor(props){
        super(props);
        this.state = {isToggleOn: true};
        this.handleClick = this.handleClick.bind(this);  //#1
        this.handleChange = this.handleChange.bind(this);
    }
    handleClick(){
        this.setState(prevState => ({
            isToggleOn: !preveState.isToggleOn
        }));
    }
    handleChange(){
        console.log(this.state.isToggleOn);
    }
    render(){
        return(
           <button onClick={this.handleClick} onChange={this.handleChange}>
                {this.state.isToggleOn ? 'ON':'OFF'}
            </button>
        )
    }
}
```
***
**构造方法中为什么要给所有的实例方法绑定this呢？**
1.代码执行过程：
上例仅仅是一个组件类的定义，当在其他组件中调用或是使用ReactDOM.render( )方法将其渲染到界面上时会生成一个组件的实例，因为组件是可以复用的，**面向对象的编程方式非常适合它的定位**。根据this指向的基本规则就可以知道，**这里的this最终会指向组件的实例**。
***
组件实例生成的时候，构造器constructor会被执行，此处着重分析一下下面这行代码：

**#1 this.handleClick = this.handleClick.bind(this);**

此时的**this指向新生成的实例**，那么**赋值语句右侧的表达式先查找this.handleClick( )**这个方法，由对象的属性查找机制（沿原型链由近及远查找）可知此处会查找到原型方法this.handleClick( ),接着执行bind(this)，此处的this指向新生成的实例，所以赋值语句右侧的表达式计算完成后，会生成一个指定了this的新方法，接着执行赋值操作，将新生成的函数赋值给实例的handleClick属性，由对象的赋值机制可知，此处的handleClick会直接作为实例属性生成。总结一下，上面的语句做了一件这样的事情：

**把原型方法handleClick( )改变为实例方法handleClick( ),并且强制指定这个方法中的this指向当前的实例。**
***
2.绑定this的重要性：
在组件上绑定事件监听器，是为了响应用户的交互动作，特定的交互动作触发事件时，监听函数中往往都需要操作组件某个状态的值，进而对用户的点击行为提供响应反馈，对开发者来说，这个函数触发的时候，就需要能够拿到这个组件专属的状态合集（例如在上面的开关组件ToggleButton例子中，它的内部状态属性state.isToggleOn的值就标记了这个按钮应该显示ON或者OFF），所以此处强制绑定监听器函数的this指向当前实例的也很容易理解。

**React构造方法中的bind会将响应函数与这个组件Component进行绑定以确保在这个处理函数中使用this时可以时刻指向这一组件的实例。**

***
3.如果不绑定this：
如果类定义中没有绑定this的指向,当用户的点击动作触发this.handleClick( )这个方法时，实际上执行的是原型方法，可这样看起来并没有什么影响，如果当前组件的构造器中初始化了state这个属性，那么原型方法执行时，this.state会直接获取实例的state属性，如果构造其中没有初始化state这个属性（比如React中的UI组件），说明组件没有自身状态，此时即使调用原型方法似乎也没什么影响。

**事实上的确是这样，这里的bind(this)所希望提前规避的，就是著名的this指针丢失的问题。**

例如使用解构赋值的方式获取某个属性方法时，就会造成引用转换丢失this的问题:

```javascript
const toggleButton = new ToggleButton();
 
import {handleClick} = toggleButton;
```
上例中解构赋值获取到的handleClick这个方法在执行时就会报错，Class的内部是强制运行在严格模式下的，此处的this在赋值中丢失了原有的指向，在运行时指向了undefined，而undefined是没有属性的。

另一个存在的限制，是**没有绑定this的响应函数在异步运行时可能会出问题**，当它作为回调函数被传入一个异步执行的方法时，同样会因为丢失了this的指向而引发错误。

如果没有强制指定组件实例方法的this，在将来的使用中就无法安心使用引用转换或作为回调函数传递这样的方式，对于后续使用和协作开发而言都是不方便的。


