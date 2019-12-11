### JS面向对象（Mosh课程5）
#### P51 Modules

```javascript
const _radius=new WeakMap();
class Circle{
	constructor(radius){
		_radius.set(this,radius);
	}
	draw(){
		console.log("circle  with radius"+ _radius.get(this));
	}
}
const c=new Circle(1);
console.log("_radius.get(c)");
c.draw();
```

#### p54 工具

如果是在nodejs中使用JavaScript就不需要关系这个；在现代的JavaScript编程中我们有两类工具分别的是转译器和打包器

### p55 bable
```javascript
➜  ~ cd desktop
➜  desktop mkdir es6-tools
➜  desktop cd es6-tools
➜  es6-tools npm init --yes
```