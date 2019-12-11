# node基础知识1

### 七、创建和删除目录：

1.删除文件：

```javascript
var fs=require("fs");//引入

fs.unlink("write.txt",function(){
    console.log("write.txt文件已经被删除了");
})
```

- fs.unlink（异步）去删除文件



2.新建和删除目录：

```javascript
fs.mkdirSync("go");//创建go文件夹
fs.rmdirSync("go");//删除go文件夹
```

- fs.mkdirSync("go");//创建go文件夹
- fs.rmdirSync("go");//删除go文件夹



3.综合的小例子🌰：

```javascript
fs.mkdir("stuff",function(){          //创建一个stuff的文件夹
    fs.readFile("readMe.txt","utf-8",function(err,data){       //读取readMe.txt内容
        fs.writeFile("./stuff/reWrite.txt",data,function(){   //在./stuff目录下写入reWrite.txt内容
            console.log("readMe的内容被重写成功了");
        })
    })
});
```



### 八、流和管道：

🐠 流的概念：

```javascript
ls  //addAndDel.js app.js       catalog.js   count.js     event.js     readMe.txt   stuff
ls | grep count  //会查找count关键字并输出 count.js
ls | grep count | grep js  //会查找count关键字然后将count作为“输入”继续查找js关键字，输出 count.js
```

🐝 创建一个读入的流并监听：

```javascript

🐌:
var fs=require("fs");
//创建一个读入流，这个流是一个事件的实例，所以拥有一些事件的特性，比如绑定一些监听函数
var curCont = fs.createReadStream(__dirname + "/readMe.txt",); 

//添加监听事件
curCont.on("data",function(chunk){
    console.log("新的流被创建了");
    console.log(chunk);
});
//<Buffer e5 a6 82 e6 9e 9c e6 b2 a1 e6 9c 89 e9 81 87 e8 a7 81 e8 bf 87 e4 bd a0 ef bc 8c e6 88 91 e7 9a 84 e4 ba ba e7 94 9f e4 bc 9a e6 98 af e6 80 8e e6 a0 ... 3513 more bytes>



//🐌🐜:加入编码符utf-8可以读取内容
var fs=require("fs");
//创建一个读入流，这个流是一个事件的实例，所以拥有一些事件的特性，比如绑定一些监听函数
var curCont = fs.createReadStream(__dirname + "/readMe.txt","utf-8"); 

//添加监听事件
curCont.on("data",function(chunk){
    console.log("新的流被创建了");
    console.log(chunk);
});

//新的流被创建了
//如果没有遇见过你，我的人生会是怎样？会是一样孤独，还是会有另一个人经过，短暂照亮，然后我们各自退回各自的轨迹，重回平静？

//你为什么不叫我等你？你也曾这样问过我。

//但爱你的人早已在那里，而且会一直在。即便他负手而立，你也知道他在等你。

//至于不爱的、爱过的，只能随他去。挽留是一个徒劳的手势，像去捕捉一只振翅欲飞的鸟。在你启程之前，你就已经离开了。

//那些看似毫无缘由的深情的确让人感动，感动于它近乎纯粹的天真和近乎愚昧的盲目。但它真的一无所求吗？还是我们想要自我蒙蔽？

//你可以嘲笑我的偏执。我怀疑一切，和其他人一样带着关心推断说，这或许是我无法获得幸福的原因。

//没有关系。我们都有看待这个世界的角度，以及探寻其后逻辑的方式。

//我不要活在假装的直觉里，那是沸乱与缠缚。偷懒的借口。

//我要的爱是清醒之后依旧心甘情愿。
```

**理解过程：**

- 通过fs.createReadStream去创建一个读入的流并赋值给一个变量，可以理解为自己造了一把空左轮枪🐣；
- curCont.on("data",function(chunk)...这个可以理解为，如果枪里"有子弹"了，那么往下执行函数"开枪"啥的。
- 输出的不是reaMe的文件，而是一个Buffer，提高了性能。
- 想要读取出readMe文件的内容，需要添加编码符utf-8

**下面是另外一种写法：**

```javascript
var fs=require("fs");

var nowContent=fs.createReadStream(__dirname + "/readMe.txt"); //创建一个读入的流
var nowWrite=fs.createWriteStream(__dirname + "/writeMd.txt"); //创建一个写入的流
nowContent.setEncoding("utf-8");  //设置编码符

var data="";

nowContent.on("data",function(chunk){   //监听读取到数据的时候
    data+=chunk;
    nowWrite.write(chunk);       //写入流写入chunk，运行后发现出现一个名为writeMd.txt的文件，内容和readMe.txt中一样
});

nowContent.on("end",function(){  //监听结束的时候输出data
    console.log(data);
});
```

写入流：

```javascript
var  writeL=fs.createWriteStream(__dirname + "/what.txt");  //创建一个写入流
writeL.write("我在哪里啊？","utf-8");   //写入内容并设置编码符
writeL.end();                    //写入结束
writeL.on("finish",function(){   //监听写入结束事件
    console.log("写入完成啦~！");
});
//然后多出一个what.txt的文件
```

管道的使用：

```javascript
var fs=require("fs");
var nowContent=fs.createReadStream(__dirname + "/readMe.txt"); //创建一个读入的流
var nowWrite=fs.createWriteStream(__dirname + "/writepipe.txt"); //创建一个写入的流
nowContent.pipe(nowWrite);//使用管道
```

- 感觉就是这个读入的流的水流，通过管道，无缝连接到了写入流，然后生成了新的文件writepipe.txt文件。