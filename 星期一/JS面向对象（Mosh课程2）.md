### JS面向对象（Mosh课程2）
#### p18 继承：
...
#### p19 原型继承：

当我们访问一个对象的属性或者方法的时候，js首先在这个对象里寻找目标，如果对象中没有找到，就到对象的原型里去找，如果还是找不到，就到原型的原型里去找，直到找到元对象。

==原型就是一个普通对象；每个对象都有原型或者父母（除了根对象没有原型以外）；==

#### p20 多级继承：

```javascript
//控制台
let a=[];
a；
//会看到a是个空的数组，点开箭头看到很多原型方法
```
==a => Array元对象 => object元对象；这种现象就叫做多级继承==


#### p21 特征描述
是否能遍历出一个实例的属性，还得看这个实例属性的特征，可以通过Object的值去设置一个属性的get和set设置。


```javascript
let person={name:"艾弗森"}；
//使用for in只能遍历person自己的属性，而无法遍历出原型中object元对象的属性
for(let key in person){
	console.log(key);
}
//使用 Object.keys(person)也是无法遍历出原型中object元对象的属性
Object.keys(person);
```

这是因为在js中所有属性都有自己的特征，某些特征阻止一个属性被枚举；
下面🌰就展示toString这个属性，它自己的特征:


```javascript
let person={name:"kobe"};
//getPrototypeOf是ES5中提供的用于得到某个对象的原型对象的标准方法。
let c=Object.getPrototypeOf(person);
//Object.getOwnPropertyDescriptor() 方法返回指定对象上一个自有属性对应的属性描述符
let descriptor=Object.getOwnPropertyDescriptor(c,"toString");
console.log(descriptor);
//控制台结果
{writable: true, enumerable: false, configurable: true, value: ƒ}

```

<font color=#2196F3 size=16 face="宋体">总结</font>

==1.getPrototypeOf是ES5中提供的用于得到某个对象的原型对象的标准方法。
2.Object.getOwnPropertyDescriptor() 方法返回指定对象上一个自有属性对应的属性描述符==

```javascript
let kk={name:"JAY"};
Object.defineProperty(kk,"name",{
writable:false
});
kk.name="JJ"
console.log(kk);  => {name:"JAY"};
```

#### p22 构造属性：


```javascript
let cc={};
cc.__proto__;
{constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …}

Object.prototype
{constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …}
```

#### p23 原型成员和实例成员：

```javascript
	function Circ(radius){
		this.radius=radius;
		this.draw=function(){
			console.log("draw");
		}
	};
	 const d1=new Circ(1);
	 const d2=new Circ(2);
	 //构造函数的原型和实例的__proto__指向同一个对象
	 Circ.prototype===d1.__proto__;
	 
	 //使用prototype改造
	function Circ1(radius){
		this.radius=radius;
		this.move=function(){
			console.logI("move");
		}
	};
	Circ1.prototype.draw=function(){
		console.log("draw");
	};
	 const d3=new Circ1(1);
	 const d4=new Circ1(2);	 
	 // Circ1原型中定义了toString方法，
	 Circ1.prototype.toString=function(){
	 	this.move();
	 	 return  "circle with radius" + this.radius;
	 }
	 
```

#### p24  lterating properties（介绍如何枚举对象原型的成员）

```javascript
//下面的例子🌰说明，原型先添加和后添加，实例都可以使用
function Circ1(radius){
		this.radius=radius;
		this.move=function(){
			console.logI("move");
		}
	};
	const a=new Circ1(1);
	a.
	Circ1.prototype.draw=function(){
		console.log("draw");
	};
	//如何枚举对象原型成员?
	// Object keys( )，只返回实例成员
	console.log(Object.keys(a));==>["radius","move"]
	
	//for in 返回是实例本身的，还有它的原型的
	for(let key in a) {
		return console.log(key);
	}
	//===>radius,move ,draw
	
	//介绍一个方法：hasOwnProperty() :检查这个实例上的属性是否是实例本身的，如果是原型的返回false，本身的就是true
	a.hasOwnProperty("draw"); ===>false;
	a.hasOwnProperty("move");===>true;
```
==总结：==


1. Object keys( )，只返回实例成员
2. for in 返回是实例本身的成员，还有它的原型的
3. hasOwnProperty() :检查这个实例上的属性是否是实例本身的，如果是原型的返回false，本身的就是true

#### p25 不要去修改内建对象



