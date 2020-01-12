### JS面向对象（Mosh课程4）

#### p40 ES6 Class
es6的class定义的类，在constructor方法内部，定义的都是这个自己的实例属性或者方法，在constructor外部定义的，例如下面例子中的draw，就会成为circle1的原型方法。 es6的class方法也是通过new去创建一个新对象的。

```javascript
function Circle(radius){
	this.radius=radius;
	this.draw=function(){
		consoloe.log("这个是draw");
	}
}

//ES6
class Circle1{
	constructor(radius){
		this.radius=radius;
		this.move=function(){
			console.log("这个是移动move");
		}
	}
	draw(){
	 	console.log("这个是draw");
	}
}
const one=new Circle(1);
const two=new Circle1(2);

typeof(Circle1);//====> "function"
```

#### p41 置顶   变量提升...是想不想记笔记了
这里面主要的还是得知道一个事情，就是class的类声明不会置顶

#### p42 静态方法
经典的oop语言中有两种类型的方法：

* 实例方法
* 静态方法：在类当中其效果，而不是在类的实例当中起作用的方法；


```javascript
class Circle1{
	constructor(radius){
		this.radius=radius;
	}
	//实例方法（原型上的）
	draw(){
	 	console.log("这个是draw");
	}
	//静态方法
	static parse(str){
		console.log(str);
		const radius=JSON.parse(str).radius;
		return new Circle1(radius);
	}
}
const o=new Circle1(1);
//o的实例里面parse方法的使用
const g=Circle1.parse('{"radius":1}')
```

#### p43 this关键字
this的指向和调用的方式有关系；

1. 方法调用：从一个对象上调用方法，通过这种方式调用，对象中的this是指向调用对象的本身的，也就是Circle；
2. 函数调用：以一种独立的方式调用函数，默认情况this就指向了windows；但是在严格模式下，就是undefined；

```javascript
const Circle=function(){
	this.draw=function(){
		console.log("这个是this关键字:"+this);
	}
}

const c=nbew Circle(1);
c.draw();//===>指向circle;

const draw=c.draw;
draw();//===>指向window
```

### p44  使用symbol来私有化构造函数的属性
一共三种方法：

* "_radius"：通过加下划线的方式去私自规定说这个是私有属性，实际上属性还是会被暴露在外面。
* 


```javascript
const _radius=Symbol();
const _draw=Symbol();
//symbol()每次调用symbol这个函数都会得到一个新的独立的单位。
class Circle{
 constructor(radius){
 	this[_radius]=radius;
 }
 //“[ ]”es6中[ ]可以计算生成属性名称，我们可以输入方括号，在里面写一个表达式，当这个表达式可以计算出结果，这个结果就会被设置成属性或者方法的名称，这里传入的_draw是一个symbol，并且可以得到值的，我们把它放到一个方括号里面，这个独立的值就会被当做方法的名称。
 [_draw]( ){
 
 }
}
const c=new Circle(1);
const key=Object.getOwnPropertySymbols(c)[0];
console.log(c[key])

```

### p45 使用ES6WeakMap(弱映射)来私有化属性
WeakMap的方法：

* set(key, value)：设置（新增/更新）键key的值为value，返回WeakMap对象。
* get(key)：读取键key的值，没有则返回undefined。
* has(key)：判断一个WeakMap对象中是否存在某个键值对，返回true/false。
* delete(key)：删除某个键值对，返回true/false。


```javascript
const _radius=new WeakMap();
const _move=new WeakMap();
class Circle{
 constructor(radius){
 //	通过set设置radius和move的属性
 	_radius.set(this,radius);
 	_move.set(this,function(){
 		console.log("这个是move")
 	})
 }
 //获取radius的方法↓  （通过get得到上面set设置的属性）
 	draw(){
 		console.log(_radius.get(this));
 	}
 	
 //调用move方法↓
 	go(){
 		_move.get(this)();
 		console.log(this.draw);
 	}	
 }
  const c= new Circle(1);
  c.go()  
```

### P46 getter和setter(es6中)



```javascript
const _radius=new WeakMap();
class Circle{
	constructor(radius){
		_radius.set(this,radius);
	}
	get radius( ){
		return _radius.get(this);
	}
	set radius(value){
		if( value<= 0) throw new Error("这里出现错误了。");
		_radius.set(this,value);
	}
}
const b=new Circle(920);
//控制台 
b.radius   //====> 得到920
设置b.radius=0的时候，会报错；b.radius=10101,再次访问的时候就会得到10101；
```

### p47 继承

```javascript
//父类构造函数
class Shape{
constructor(color){
	this.color=color;
}
	move(){
		console.log("这个是move");
	}
}
//子类构造函数
class Circle extends Shape{
//这里要是有constructor属性就必须用super去继承父类的属性
//super关键字用于访问和调用一个对象的父对象上的函数。
	constructor(color,radius){
		super(color);
		this.radius=radius;
	}
	draw(){
		console.log("draw");
	}
}
const c= new Circle("blue",920);
```

### p48 方法重写
如果想用shape中的move方法，就得像下面这样调用super().move()方法。

```javascript
class Shape{
	move(){
		console.log("这个是Shape");
	}
}

class Circle extends Shape{
	move(){
		super.move();
		console.log("这个是Circle")
	}
}
const c=new Circle();
```
