### JS面向对象（Mosh课程1）
#### p1：面向对象是一种编程风格：
#### p2：四个核心概念：封装、继承、抽象和多态：
1：对象里面的变量成为属性，函数成为方法


2: 封装：将属性和操作属性的方法组合在对象中，就叫做封装


```javascript
雇佣算工资的小例子🌰：
let emmployee={
	baseSalary:3000,
	overTime:10,
	rate:20,
	getWage:function(){
		return this.baseSalary + （this.overTime * this.rate);
	}
}
```
函数具有多个参数，而使用面向对象 方式时，方法所需的参数最终越来越少

3：抽象：向外部隐藏一些属性和方法，带来的好处就是使接口更简单

4：继承：继承是一种机制，让程序员减少冗余的代码

5：多态：多态是一种技术，让你摆脱冗长的if-else、switch-case语句

#### p6 对象的创建：
1：对象的属性是用来存储数值的，方法是用来定义一些逻辑的。

```javascript
 let circle={
 	radius:1,
 	location:{
 		x:1,
 		y:1
 	},
 	draw:function(){
 		console.log("draw")
 	}	
 }
```
#### p7 工厂函数：
就是直接通过函数去return一个对象，然后创建对象的时候直接调用函数就行了。

```javascript
function createCircle(radius){
	return {
		radius,
		raw:funciton(){
			console.log("draw");
		}
	}
}
	const circle=createCircle(1);
```

#### p8构造函数：
使用构造函数new一个新对象，分为四步：

1. 函数内部建立一个私有域，悄悄的建立一个局部变量，是空对象{}
2. 函数将自己的上下文设置成这个空对象{}，也就是将函数中的this表示这个空对象。
3. 函数执行所有语句
4. 所有的有语句执行完毕后，函数返回这个对象，也就是将自己的this返回


```javascript
function Circle(radius){
	console.log("this",this);
	this.radius=radius;
	this.draw=function(){
		console.log("draw");
	}
}
const another=new Circle(1);
```
#### p9构造函数属性：
```javascript
function Circle(radius){
	console.log("this",this);
	this.radius=radius;
	this.draw=function(){
		console.log("draw");
	}
}
const another=new Circle(1);
another.constructor; // => Circle(){...}
```
#### p10 值类型 vs引用类型


1.值类型：number，string，boolean，symbol，undefined，null

2.引用类型：object，function，array


```javascript
//值类型例子🌰：
let x=10；
let y=20；
x=20；
//控制台  x=20; y=10;他们是独立的

//引用类型例子🌰：
let x={value:10};
let y=x;  
x.value=20;
//控制台   x=20;y=20
//引用类型不储存在变量里，变量中只是储存的对象在内存中的地址。
//上面拷贝x给y的操作实际上是把对象x的地址给了y

//另一个例子：
let number=10；
function increase(number){
	number++;
}
increase(number);
console.log(number) //==>   10；

//这是两个独立的变量
```
==值类型复制值，引用类型复制引用==

#### p11 添加和删除属性：

动态的，添加和删除属性是自由的


```javascript
function Circle(radius){
	console.log("this",this);
	this.radius=radius;
	this.draw=function(){
		console.log("draw");
	}
}
const another1=new Circle(1);
another1.location={x:1};
another1.constructor

//删除属性用delete
delete another1.location；
```

#### p12 枚举属性：
利用for-in


```javascript
function Circle(radius){
	this.radius=radius;
	this.draw=function(){
		console.log("draw");
	}
}
const another3=new Circle(1);

//for in 遍历对象
for( let key in another3){
	if(typeof another3[key] !== "function")
	console.log(key,another3[key]);
}

//Object.keys()方法:使用数组返回传入对象所有的键
const key3=Object.keys(another3);
console.log(key3);

//检查某个属性是否在对象中
if("radius" in another3){
	console.log(“radius 存在于another3中”)
}
```

#### p13 抽象：隐藏细节，只暴露必须
==抽象：隐藏细节和复杂的部分，只显示或者暴露必要的部分。（这里我们想要隐藏defaultLocation和computeOptimumLocation这两个）==


```javascript
function Circle(radius){
	this.radius=radius;
	this.defauleLocation={
		x:0,
		y:0	
	};
	this.computeOptimumLocation=function(factor){
		//...
	};
	this.draw=function(){
		this.computeOptimumLocation(0.1);
		console.log("draw");
	}
}
const circle=new Circle(1);

```

#### p14 私有属性和方法：
利用了闭包，让defaultLocation和computeOptimumLocation只能通过draw方法

```javascript
function Circle(radius){
	let color="red";  //当离开这个函数的时候，这个私有变量就离开了作用域，也就死掉了
	this.radius=radius;
	//所以不再将defaultLocation和computeOptimumLocation设置成属性，而是用let设置成内部变量
	let defauleLocation={
		x:0,
		y:0	
	};
	let computeOptimumLocation=function(factor){
		//...
	};
	this.draw=function(){
		this.computeOptimumLocation(0.1);
		console.log("draw");
	}
}
const circle=new Circle(1);

```

#### p15 getters和setters：
现在是想要某个实例显示这个defaultLocation

```javascript
function Circle(radius){
	console.log("this",this)
	this.radius=radius;
	let defaultLocation={
		x:0,
		y:0	
	};
	this.draw=function(){
		console.log("draw");
	};
	Object.defineProperty(this,"cc",{
		//当外界访问cc的时候，get下的这个函数将会被调用
		get:function(){
			return defaultLocation
		},
		//想从外界设置这个值
		set:function(value){
			if(!value.x || ! value.y){
				throw new Error("invalid ");
			}
			defaultLocation=value;
		}
	})
}
const circle=new Circle(1);
```





#### 