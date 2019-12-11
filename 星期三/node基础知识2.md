# node基础知识2

### 九、web服务器part1 介绍：

```javascript
var http=require("http");

var server=http.createServer(function(request,response){    //request,response都是流的实例
    console.log("请求发送过来了");
    response.writeHead(200,{"Content-Type":"text/plain;charset=utf-8"});
    response.write("喜极而泣，这是个我的第一个服务器");
    response.end();
});

server.listen("3000",function(){
    console.log("3000端口运行了我搭建的第一个服务器");
});
```



- request,response都是流的实例



### 十、web服务器part2 响应json：

如何响应一个json给客户端

```javascript
var http=require("http");
var onRequert=function (request,resoponse){
    console.log("我要给客户端响应一个json");
    resoponse.writeHead(200,{"Content-Type":"application/json;charset=utf-8"});
    var myObj={
        name:"艾弗森",
        number:3,
        company:"NBA"
    };
    resoponse.end(JSON.stringify(myObj));
};

var server=http.createServer(onRequert);
server.listen("8000",function(){
    console.log("成功响应一个json给客户端");
})
//浏览器的locallhost:8000 显示结果为：
//{"name":"艾弗森","number":3,"company":"NBA"}
```

这里插入下JSON的两个常用方法的介绍：

- JSON.stringify():把JS对象转换成JSON字符串
- JSON.parse():把JSON字符串转换成JS对象



### 十一、web服务器part3 响应HTML页面：

```javascript
var http=require("http");

var onRequest=(request,response)=>{
    console.log("准备响应html啦ヽ(￣▽￣)و");
    response.writeHead(200,{"Content-Type":"text/html;charsetutf-8"});
    var html=`<!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>这个是我创建的html页面</title>
    </head>
    <body>
        你好啊，少年郎~
    </body>
    </html>`;
    response.end(html);
}

var server=http.createServer(onRequest);
server.listen("3000",function(){
    console.log("哑吼~！");
})

//locallhost:3000显示：你好啊，少年郎~
```

上面这么写未免有些太粗暴了🦁，尤其是html那部分，太长了，那么如何改进呢？

```javascript
var http=require("http");
var fs=require("fs");

var onRequest=(request,response)=>{
    console.log("准备响应html啦ヽ(￣▽￣)و");
    response.writeHead(200,{"Content-Type":"text/html"});
    var myRead=fs.createReadStream(__dirname + "/index.html","utf8");
    myRead.pipe(response);   //将创建的读取流，引流至响应中去ヽ(￣▽￣)و
}

var server=http.createServer(onRequest);
server.listen("3000",function(){
    console.log("哑吼~！");
})
```

- 通过fs.createReadStream创建一个流，然后通过管道pipe引流到响应中去。



### 十二、web服务器part4 用模块化思想组织代码：

封了一个startServer模块，然后在别的文件中对其进行使用

```javascript
//http-p4.js
var http=require("http");
var fs=require("fs");

function startServer(){
    var onRequest=(request,response)=>{
        console.log("准备响应html啦ヽ(￣▽￣)و");
        response.writeHead(200,{"Content-Type":"text/html"});
        var myRead=fs.createReadStream(__dirname + "/index.html","utf8");
        myRead.pipe(response);   //将创建的读取流，引流至响应中去ヽ(￣▽￣)و
    }
    
    var server=http.createServer(onRequest);
    server.listen("3000",function(){
        console.log("哑吼~！");
    })
};

module.exports.startServer=startServer;  //导出


//app.js对上面导出模块的使用
var server=require("./http-p4");
server.startServer();
```



### 十三、web服务器part5 路由：

```javascript
var http=require("http");
var fs=require("fs");

var onRequest=(request,response)=>{
    console.log(request.url);
    if(request.url==="/" || request.url==="/home"){
        response.writeHead(200,{"Content-Type":"text/html"});
        fs.createReadStream(__dirname + "/index.html","utf8").pipe(response);
    }else if(request.url==="/review"){
        response.writeHead(200,{"Content-Type":"text/html"});
        fs.createReadStream(__dirname + "/review.html","utf8").pipe(response);
    }else if(request.url==="go"){
        response.writeHead(200,{"Content-Type":"text/html"});
        fs.createReadStream(__dirname + "/go.html","utf8").pipe(response);
    }else{
        response.writeHead(200,{"Content-Type":"text/html"});
        fs.createReadStream(__dirname + "/404.html","utf8").pipe(response);
    }
}

var server=http.createServer(onRequest);
server.listen("3000",function(){
    console.log("哑吼~！");
})
```

- 就是通过request.url进行判断，从而响应不同的页面。



### 十四、web服务器part6 重构路由代码：

这个重构可能有点长：首先来看下server.js

```javascript
//server.js
var http=require("http");
function startServer(route,handler){
    var onRequest=(request,response)=>{
        route(request.url,handler,response);
    }
    var server=http.createServer(onRequest);
    server.listen("3000",function(){
        console.log("哑吼~！");
    })
};

module.exports.startServer=startServer;

```

再来看下route.js

```javascript
var fs=require("fs");
function route(pathname,handler,response){
    console.log("o(￣▽￣)ｄ"+ pathname);
    if(typeof handler[pathname] === "function"){
        handler[pathname](response);
    }else{
        console.log("no pathName match it");
        response.writeHead(200,{"Content-Type":"text/html"});
        fs.createReadStream(__dirname + "/404.html","utf8").pipe(response);
    }
};
module.exports.route=route;
```

然后是关于路由里面的handler(就是管着往哪跳转的)

```javascript
var fs=require("fs");

function home(resp){
    console.log("home page");
    resp.writeHead(200,{"Content-Type":"text/html"});
    fs.createReadStream(__dirname + "/index.html","utf8").pipe(resp);
}
function review(resp){
    console.log("review page");
    resp.writeHead(200,{"Content-Type":"text/html"});
    fs.createReadStream(__dirname + "/review.html","utf8").pipe(resp);
}
function go(resp){
    resp.writeHead(200,{"Content-Type":"text/html"});
    fs.createReadStream(__dirname + "/go.html","utf8").pipe(resp);
}

module.exports={
    home,review,go
}
```

最后就是app.js入口文件啦ヽ(￣▽￣)و

```javascript
var server=require("./server");
 var {route}=require("./route");//es6 解构赋值
var handle=require('./handle');

var handler={};
handler["/"]=handle.home;
handler["home"]=handle.home;
handler["/go"]=handle.go;
handler["/review"]=handle.review;

console.log(typeof(route)+"类型");  //route是一个函数类型

server.startServer(route,handler);
```

我真的是太优秀了︿(￣︶￣)︿，唉，没办法，就是这么优秀ヽ(￣▽￣)وHiaHiaHia....（一副小人得志的嘴脸🤓）



### 十五、web服务器part7 使用get或者post请求发送数据：

1.get方法：

```javascript
//server.js

var http=require("http");
var url=require("url");       //引入了url的库

function startServer(route,handler){

    var onRequest=(request,response)=>{
        var pathname=url.parse(request.url).pathname;
        console.log("我是路径/后面的名字：(不包括query)" + pathname );

        var params=url.parse(request.url,true).query;    //解析这个url地址栏3000端口之后的内容；true-->返回一个对象；false--->返回一个字符串
        console.log(params);
      
        route(pathname,handler,response,params);  
    }
    var server=http.createServer(onRequest);
    server.listen("3000",function(){
        console.log("哑吼~！");
    })
};

module.exports.startServer=startServer;

```

- 引入url库；通过url.parse方法对url地址栏后面的内容进行解析
- url.parse参数传入true的时候解析返回----> 对象
- url.parse参数传入false的时候解析返回----> 字符串
- 然后将params传入route

下面是route.js:

```javascript
//route.js

var fs=require("fs");

function route(pathname,handler,response,params){
    console.log("o(￣▽￣)ｄ"+ pathname);
  
    if(typeof handler[pathname] === "function"){
        handler[pathname](response,params);
    }else{
        console.log("no pathName match it");
        response.writeHead(200,{"Content-Type":"text/html"});
        fs.createReadStream(__dirname + "/404.html","utf8").pipe(response);
    }
};
module.exports.route=route;
```

接着是handle.js:

```javascript
var fs=require("fs");

function home(resp,params){
    console.log("home page");
    resp.writeHead(200,{"Content-Type":"text/html"});
    fs.createReadStream(__dirname + "/index.html","utf8").pipe(resp);
}
function review(resp,params){
    console.log("review page");
    resp.writeHead(200,{"Content-Type":"text/html"});
    fs.createReadStream(__dirname + "/review.html","utf8").pipe(resp);
}
function go(resp,params){
    resp.writeHead(200,{"Content-Type":"text/html"});
    fs.createReadStream(__dirname + "/go.html","utf8").pipe(resp);
}
function api_records(resp,params){
    resp.writeHead(200,{"Content-Type":"application/json"});
    resp.end(JSON.stringify(params));
}

module.exports={
    home,review,go,api_records
}
```

最后是入口文件app.js：

```javascript
var server=require("./server");
 var {route}=require("./route");//es6 解构赋值
var handle=require('./handle');

var handler={};
handler["/"]=handle.home;
handler["home"]=handle.home;
handler["/go"]=handle.go;
handler["/review"]=handle.review;
handler["/api_records"]=handle.api_records;
console.log(typeof(route)+"类型");  //route是一个对象类型

server.startServer(route,handler);
```



2.post方法：

```html
//index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>这个是我创建的html页面</title>
</head>
<body>
    你好啊，少年郎~
    <form action="/api_records" method="POST"> 
        name:<input type="text" name="name"/><br/>
        age:<input type="text" name="age" /><br/>
        <input type="submit"  value="提 交"/>
    </form>
</body>
</html>
```

```javascript
//server.js

var http=require("http");
var url=require("url");
var querystring=require("querystring");  //引入的一个querystring库

function startServer(route,handler){

    var onRequest=(request,response)=>{
        var pathname=url.parse(request.url).pathname;
        console.log("我是路径/后面的名字：(不包括query)" + pathname );

        var data="";
        request.on("error",function(error){
            console.log(error);
        }).on("data",function(chunk){
            data+=chunk;              //请求数据的时候将数据组合在一起
        }).on("end",function(){
            if(request.method==="POST"){
                if(data.length > 1e6){
                   request.connection.destroy();
                }
                route(pathname,handler,response,querystring.parse(data));//对data进行解析并传入
            }else{
                var params=url.parse(request.url,true).query;    //解析这个url地址栏3000端口之后的内容；true-->返回一个对象；false--->返回一个字符串
                console.log(params); 
                route(pathname,handler,response,params);
            }
        })
    }
    var server=http.createServer(onRequest);
    server.listen("3000",function(){
        console.log("哑吼~！");
    })
};

module.exports.startServer=startServer;

```



```javascript
//route.js
var fs=require("fs");
function route(pathname,handler,response,params){
    console.log("o(￣▽￣)ｄ"+ pathname);
    if(typeof handler[pathname] === "function"){
        handler[pathname](response,params);
    }else{
        console.log("no pathName match it");
        response.writeHead(200,{"Content-Type":"text/html"});
        fs.createReadStream(__dirname + "/404.html","utf8").pipe(response);
    }
};
module.exports.route=route;
```



```javascript
//handle.js

var fs=require("fs");

function home(resp,params){
    console.log("home page");
    resp.writeHead(200,{"Content-Type":"text/html"});
    fs.createReadStream(__dirname + "/index.html","utf8").pipe(resp);
}
function review(resp,params){
    console.log("review page");
    resp.writeHead(200,{"Content-Type":"text/html"});
    fs.createReadStream(__dirname + "/review.html","utf8").pipe(resp);
}
function go(resp,params){
    resp.writeHead(200,{"Content-Type":"text/html"});
    fs.createReadStream(__dirname + "/go.html","utf8").pipe(resp);
}
function api_records(resp,params){
    resp.writeHead(200,{"Content-Type":"application/json"});
    resp.end(JSON.stringify(params));
}

module.exports={
    home,review,go,api_records
}
```





```javascript
//app.js

var server=require("./server");
 var {route}=require("./route");//es6 解构赋值
var handle=require('./handle');

var handler={};
handler["/"]=handle.home;
handler["home"]=handle.home;
handler["/go"]=handle.go;
handler["/review"]=handle.review;
handler["/api_records"]=handle.api_records;
console.log(typeof(route)+"类型");  //route是一个对象类型

server.startServer(route,handler);
```



### 十六、包管理器NPM：

贴个淘宝镜像的网站：https://npm.taobao.org/



### 十七、package.json:

package.json:记录所有安装过包的信息的文件。

- 命令行中npm init   ：初始化一个项目的安装信息
- 安装express例子： npm install --save express 



### 十八、nodemon：

就是代码变化的时候，服务期自动重启