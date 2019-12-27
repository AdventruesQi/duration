### ES6中class类学习笔记：
#### 1️⃣：创建类和生成类：
```javascript
class Star{
   constructor(name){
     this.name=name;
   }
}
var g=new Star("ldh");
console.log(g);
```
* 通过class类创建类，实际上class是个函数
* 类里面有个constructor函数，可以接收传递过来的参数，同时返回实例对象
* constructor函数只要是通过new生成实例的时候，就会自动调用这个constructor函数，即使我们不写这个constructor函数，类也会自动生成这个函数。

### 2️⃣：类中添加共有方法：

```javascript
class Star{
   constructor(name){
     this.name=name;
   }
   //添加的共有方法
  sing(song){
    console.log("共有方法"+song);
  }
}
var g=new Star("ldh");
console.log(g.sing("逝去的歌"));  //===>得到逝去的歌
```

* 类里面所有的函数不需要写function;
* 多个方法之间不需要添加逗号分隔；

### 3️⃣类继承的extends和super关键字：
* 通过extends完成两个类之间的继承；


```javascript
class Father{
  constructor(x,y){
    this.x=x;
    this.y=y;
  }
  sum(){
    console.log(this.x+this.y);
  }
}
class Son extends Father{
  constructor(x,y){
    super(x,y);   //用用父类中的构造函数constructor，并将参数x和y传递过去
  }  
}
var son=new Son(1,920);
son.sum();
```
### 4️⃣：super调用父类普通函数以及继承中属性方法查找原则：
通过super函数来调用父类的普通函数


```javascript
class Father{
  say(){
    return "我是爸爸"
  }
}
class Son extends Father{
  say(){
    console.log(super.say()+"的儿子")
  }
}
var son=new Son();
son.say();//====>"我是爸爸的儿子"
```

### 5️⃣:super必须放到子类的this之前：（必须先紧着老子，然后才是自己）

```javascript
class Father{
  consrtuctor(x,y){
    this.x=x;
    this.y=y;
  }
  sum(){
    console.log(this.x+this.y);
  }
}
class Son extends Father{
  constructor(x,y){
    super(x,y); //父类this必须在子类之前调用
    this.x=x;
    this.y=y;
  }
  substract(){
    console.log(this.x-this.y);
  }
}
var son=new Son(920,1);
son.sum();//====>921
son.substract();//=====>919
```

### 6️⃣：使用类的几个注意点：
* ES6中没有变量提升，必须先有类，然后才能通过类来实例化实例。
* 类里面的共有属性和方法一定要加this使用。

### 7️⃣类里面的this指向问题：

* constructor里面的this指向的是创建的实例对象。
* 方法里面的this指向的是方法的调用者。

```javascript
//html
<button></button>
//js
var that;
class Star{
  constructor(name,age){
 用that保存下来this，然后在下面的方法中用that.name来获取正确的name值
    var that=this;  
    this.name=name;
    this.age=age;
    this.btn=doucment.querySelcet("button");
    this.btn.onClick=this.sing;   //this.sing的this指向的是this.btn，this.btn的this指向的是组件实例。
  }
  sing(){
    console.log(this);//指向方法的调用者
    console.log(this.name);//undefined
    console.log(that.name);//得到正确的name
  }
}

```

