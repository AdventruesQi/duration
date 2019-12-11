# node基础知识

### 一、node的介绍以及环境搭建：

node就是一个服务端的javascript，运行在chrom的v8 -javascript引擎中；
javascript是一门脚本语言，需要解析器才能运行的，浏览器就充当了那个解析器的角色，chrom浏览器中，这个解析器就是一个v8引擎，能够高效的把js语言转换成机器码，进行运行。

vscode的拓展工具总安装terminal拓展工具，安装过后就可以在“鼠标🖱”右键就会多出open in intergrated treminal的选项。 使用这项就会打开编辑器的集成终端，然后通过node +"运行的文件名字"对文件进行运行。

```javascript
node app.js
```



### 二、全局对象：

- Console

- setTimeout
- setInterval
- __dirname   输出当前的目录   /Users/xuyongqi/Desktop/basic_node/basic_node/file
- ls 输出当前文件夹的所有文件
- __filename  输出当前文件的路径名 
- require
- exports

### 三、回调函数：（跳过）

### 四、模块：

file文件夹下有两个js文件：

```javascript
//count.js🍋
var counter=function(arr){
    return "这里有" + arr.length +"个element在数组中"
}

module.exports =counter;

//app.js🍎
var counter=require("./count");
console.log(counter(["javascript","python","node","c++"]));  
//这里有4个element在数组中
```

- counter.js中module.exports代表要暴露出去的内容（暴露出一个函数）
- app.js中require的是上面暴露出的内容（函数），所以将他赋值给一个变量直接调用就可以啦。

🌰例子2：

```javascript
//count.js
var counter=function(arr){
    return "这里有" + arr.length +"个element在数组中"
}
var adder=function(a,b){
    return `a+b 等于${a+b}`;
}
var pi=3.14

module.exports.counter =counter;
module.exports.adder =adder;
module.exports.pi =pi;

module.exports={
  counter,adder,pi
};

//app.js
var go=require("./count");
console.log(go.counter(["javascript","python","node","c++"]));
//这里有4个element在数组中
console.log(go.adder(1,920));
//a+b等于921
```

- module.exports+"名字"等形式暴露出定义的内容。
- 通过require("./count")对count.js中的所有的内容进行引入
- module.exports={counter,adder,pi }和一个个导出一样可行。



### 五、事件：

🥕：

```javascript
var events= require("events");  //引入事件库
var myEmmiter=new events.EventEmitter(); //创建一个事件对象

myEmmiter.on("myEvents",(msg)=>{   //给指定事件名绑定事件
    console.log(msg);
});
myEmmiter.emit("myEvents","去吧，皮卡丘~！")  //触发事件
```

🍙：

```javascript
var util=require("util");   //引入工具库
var events= require("events");  //引入事件库

var Person=function(name){    
    this.name=name;
}
util.inherits(Person,events.EventEmitter);  //使用工具库，让Person对象具有绑定事件的属性。     
var xiaoming=new Person("xiaoming");
var lili=new Person("lili");
var lucy=new Person("lucy");
var person=[xiaoming,lili,lucy];

person.forEach(element => {      //使用forEach遍历每个element，给它绑定时间
    element.on("speak",function(message){
        console.log(message);
    })
});

xiaoming.emit("speak","hi~！我叫小明");
lili.emit("speak","我叫莉莉，小明的童鞋");
lucy.emit("speak","这个是鳄鱼么？不是，我是露西");

```



### 六、读写文件（同步，异步）：

1.文件读取的方法（同步）：🍝

```javascript
//readMe.txt
我被读取了。ヽ(￣▽￣)

//读取文件
var fs=require("fs");
 var goread=fs.readFileSync("readMe.txt","utf-8");
 console.log(goread);
//控制台结果：
//我被读取了。ヽ(￣▽￣)و

```

- fs.readFileSync读取文件方法



2.文件写入的方法（同步）：🥘

```javascript
fs.writeFileSync("write.txt",goread);
```

- fs.writeFileSync文件写入的方法



3.文件的读取（异步）：🍕

```javascript
 var fs=require("fs");
 
var goneread=fs.readFile("write.txt","utf-8",function(err,data){
    console.log(data);
});

console.log("finished");

//控制台
//finished
//打印data

```

- 控制台先打印的是finished，然后打印的出data的内容。具体过程如下：
- 这样是为了维护时间队列； var fs=require("fs");这一行会先执行；
- 执行var goneread=...他就会在时间队列那注册一个事件，告诉这个事件队列，我将要去读一个文件，但是那个回调函数还没有被马上执行；
- 然后去完成主线程console.log("finished");
- 当主线程空闲的时候，他就会去找事件队列里面的事件，把它取出来，在同线程中发起一个线程，去执行事件队列里面的事件
- 执行完时候再告诉主线程，我已经成功了；

**如果有一些同步的方法在主线程中运行，那么会阻塞所有的操作。**





