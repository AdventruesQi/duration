### JS面向对象（Mosh课程3）
#### P28 创建你自己的原型继承：


```javascript
function Circle(radius){
	this.radius=radius;
};
Circle.prototype.draw=function(){
	console.log("draw");
};
//Circle.prototype.duplicate=function(){
//	console.log("duplicate")
//}

//我们不想用这种方法去一直写duplicate方法----想象我们可以创建一个Shape对象，
//之后把duplicate方法放进去，然后让Circle继承Shape对象.
function Shape(){

}
Shape.prototype.duplicate=function(){
	console.log("duplicate")
}
Circle.prototype=Object.create(Shape.prototype);

const s=new Shape();
const c=new Circle(1);

```


这里再讲一下那个Object.create()方法：Object.create()方法创建一个新对象，使用现有的对象来提供新创建的对象的__proto__。[MDN学习链接](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create)

```javascript
const person = {
  isHuman: false,
  printIntroduction: function () {
    console.log(`My name is ${this.name}. Am I human? ${this.isHuman}`);
  }
};

const me = Object.create(person);

me.name = "Matthew"; // "name" is a property set on "me", but not on "person"
me.isHuman = true; // inherited properties can be overwritten

me.printIntroduction();
// expected output: "My name is Matthew. Am I human? true"
```


###### p29 重新设置constructor
这里我们要说明一个问题，就是，p29中的原型方法里面会会出现一个问题，在重新设置了“整个原型”对象之后，需要重新设置constructor属性


```javascritp
//实例c的constructor指向了shape；
//c.constructor指向的是Shape构造函数，而不是Circle构造函数；
c.prototype.constructor=Circle

```

###### p30 calling the super Constructor
如果单纯的直接在Circle内部对shape进行调用，那么不会在c的实例中看到color属性，但是window里面会看到color属性。

```javascript
//Shape 
function Shape(color){
	this.color=color;
}
Shape.prototype.duplicate=function(){
	console.log("duplicate")
}
//Circle
function Circle(radius,color){
	//Shape(color);
	Shape.call(this,color);
	this.radius=radius;
};
//继承父组件的shape的原型
Circle.prototype=Object.create(Shape.prototype);
//归正Circle原型的指针方向
Circle.prototype.costructor=Circle
//添加自己的原型方法
Circle.prototype.draw=function(){
	console.log("draw");
};
//实例
const s=new Shape();
const c=new Circle(1,"blue");

```

###### P31 中间函数继承
就是重构下面梁行代码，让他们变成可复用的函数；

Circle.prototype=Object.create(Shape.prototype);
Circle.prototype.costructor=Circle

⏬

```javascript

function extends(Child,Parent){
	Child.portotype=Object.create(Parent.prototype);
	Child.prototype.constructor=Child;
}
```

```javascript
function extends(Child,Parent){
	Child.portotype=Object.create(Parent.prototype);
	Child.prototype.constructor=Child;
}
//Shape 
function Shape(color){
	this.color=color;
}
Shape.prototype.duplicate=function(){
	console.log("duplicate")
}
//Circle
function Circle(radius,color){
	//Shape(color);
	Shape.call(this,color);
	this.radius=radius;
};

//Circle.prototype=Object.create(Shape.prototype);
//Circle.prototype.costructor=Circle
entends(Circle,Shape);

Circle.prototype.draw=function(){
	console.log("draw");
};

//新增加的Square函数,想让square也继承自shape
function Square(size){
	this.size=size;
}
//Square.prototype=Object.create(Shape.prototype);
//Square.prototype.costructor=Square;
extends(Square,Shape);
//实例
const s=new Shape();
const c=new Circle(1,"blue");
const sq=new Square(12);
```

###### P32方法延伸
就是如果我想要让Circle有一个自己的duplicate方法，那么就应该让Circle重写一个prototype方法，但是定义该方法的时候需要在extends(Circle,Shape)方法之后；

```javascript
function extends1(Child,Parent){
	Child.portotype=Object.create(Parent.prototype);
	Child.prototype.constructor=Child;
}
//Shape 
function Shape(color){
	this.color=color;
}
Shape.prototype.duplicate=function(){
	console.log("duplicate")
}
//Circle
function Circle(radius,color){
	//Shape(color);
	Shape.call(this,color);
	this.radius=radius;
};
extends1(Circle,Shape);
Circle.prototype.duplicate=function(){
//例子1 调用自己写的duplicate方法
	console.log("嗯，这个就是我重写的方法");
//例子2	使用Shape里的duplicate方法
	//Shape.prototype.duplicate.call(this);	
}
 const c=new Circle();

```

###### p33 多态的使用场景
多态是个啥，比如下面例子的duplicate方法，继承的原型里有duplicate方法，可是circle和Square构造函数都自己重写了自己的duplicate方法，这个就叫做多态。


```javascript
function extends1(Child,Parent){
	Child.portotype=Object.create(Parent.prototype);
	Child.prototype.constructor=Child;
}
//Shape 
function Shape(color){
	this.color=color;
}
Shape.prototype.duplicate=function(){
	console.log("duplicate")
}
//Circle
function Circle(radius,color){
	//Shape(color);
	Shape.call(this,color);
	this.radius=radius;
};
extends1(Circle,Shape);
Circle.prototype.duplicate=function(){
	console.log("嗯，这个就是我重写的方法");
}
//Square
function Square(){

}
extends1(Square,Shape);
Square.prototype.duplicate=function(){
	console.log("这个是Square的自写原型方法")
}
 const c=new Circle();
 
 const oo=[new Circle(),new Square()];
 for(let shape of oo){
 	shape.duplicate();
 }
```
###### p34 当我们使用继承
继承是不错，但是要知道，好的组合胜过继承。

##### p35 组合(混合)
[ES6--三个点的用法](https://www.jianshu.com/p/06c934317bc0)

```javascript
//可以把object的逻辑放到Mixin中去
//第一个”...“是剩余操作符---核心就是"打包"（多个变成一个数组），第二个是拓展操作符---核心是”打散“（一个变成多个）；
function mixin(target,...rest){
	Objcet.assign(target,...rest)
}

const canEat={
	eat:function(){
		this.hunger--;
		console.log("eating");
	}
}

const canWalk={
	wlak:function(){
		console.log("walk");
	}
}

const canSwim={
	swim:funciton(){
		console.log("swim");
	}
}
const person=Object.assign({},canEat,canWalk);
console.log(person);

//应用于构造函数中的原型中的例子；

//人
function Person (){

}
Object.assign(Person.prototype,canEat,canWalk);
const g=new Person();

//金鱼
function GoldFish(){

}
Objcet.assgn(GoldFish,canEat,canSwim);

const fish=new GoldFish();
console.log(fish);

```