## cookie

在浏览器上保留一些数据；

每次请求都会带去服务器；

键值对的形式存在，可以设置多个

不同域名之间的cookie不能共享

```tex
1. 不安全（浏览器上的cookie数据可以在可在客户端进行修改）
2. 有限（大小有限制，<4K）
```



### 属性

```js
max-age和expires设置过期时间
Secure只在https的时候发送
HttpOnly属性设置后，则无法通过document.cookie访问cookie的内容
```



### cookie的设置与发送

- 通过`Set-Cookie`设置

```js
const http = require('http');
const fs = require('fs');
http.createServer((req, res) => {
    if(req.url === '/') {
        let content = fs.readFileSync('./test.html', 'utf-8');
        res.writeHead("200", {
            "Content-Type": "text/html",
            "Catch-Control": "max-age=200",
            "Set-Cookie": ["id=123", "name=xnn"]
            //"Set-Cookie": ["id=123;max-age=10", "name=xnn;httpOnly"]
        });
        res.end(content);
    }
}).listen(8888);
```

![image-20191118154638294](C:\Users\xnn\AppData\Roaming\Typora\typora-user-images\image-20191118154638294.png)



- 通过res.cookie来进行设置。

```js
res.secret=字符串; //设置签名
res.cookie(name, value, {
	path: 有效设置path, 
    maxAge: 有效期设置ms, 
    signed: true/false //是否进行数字签名，默认是false
});
```



```js
const express = require('express');

var server = express();
server.listen(9988);

server.use('/aaa', (req, res) => {
    res.cookie("xnn", "123", {path: '/aaa', maxAge: 24*3600*1000});
   res.send('ok');
   res.end();
});
```



### cookie的读取

需要中间件 -- cookie-parser

```js
server.use(cookieParser('签名秘钥'))；//解析签名
req.signedCookies //-- 获取解析后的签名的cookie
req.cookies //-- 获取未签名的cookie
```



```js
const express = require('express');
const cookiePaser = require('cookie-parser');

var server = express();
server.listen(9988);

server.use(cookiePaser());
server.use('/aaa', (req, res) => {
    res.send(req.cookies); //{'xnn': '123'}
    res.end();
});
```

 

### cookie签名

用来对cookie数据进行数字签名，防止客户端对cookie数据进行修改。

```js
var express = require('express');

var server = express();
server.listen(7777);

server.use('/abc', (req, res) => {
    req.secret = 'machelnicai'; //设置签名
    res.cookie('xnn','say hello to world', {
        signed: true //对该cookie进行数字签名操作，签名后的cookie值为：s%3Asay%20hello%20to%20world.q90OAw39%2BD3pdlwuQV2ZZFirFaQ7v%2BOXklQnzkmTiog
   });

    res.send('ok');
    res.end();
});
```



### 解析cookie签名

```js
var express = require('express');
var cookieParse = require('cookie-parser');

var server = express();
server.listen(7777);

//用签名秘钥解析签名
server.use(cookieParse('machelnicai'));
server.use('/abc', (req, res) => {
    /*req.secret = 'machelnicai';
    res.cookie('xnn','say hello to world', {
        signed: true
    });
    res.cookie('xnn2','value2');*/
    console.log(req.signedCookies);//获取有签名的cookie并解析。{ xnn: 'say hello to world' }
    console.log(req.cookies);  //获取没有签名的cookie，{ xnn2: 'value2' }
    res.send('ok');
    res.end();
});
```



### 删除cookie

```js
res.clearCookie(名字);
```



### cookie加密

使用中间件 -- cookie-encrypter





## session

在服务器端用来保存数据,不能独立存在，基于cookie实现

​	安全 、 不限制大小



### 原理

cookie中会有一个session的ID，服务器利用浏览器带来的sessionID，找到对应的session文件进行读取或者写入操作。

隐患：session劫持



### session的运用

需要中间件 -- cookie-session

```js
server.use(cookieParser());
server.use(cookieSession({    
    name: 'sess',    
    keys: ['aaa', 'bbb', 'ccc'],
    maxAges: 24*3600*1000 //有效期为1天
}));
```

 ```js
const express = require('express');
const cookieParser = require('cookie-parser');
const cookieSession = require('cookie-session');

let server = express();
server.listen(8877);

//使用之前先解析cookie
server.use(cookieParser());
server.use(cookieSession({
    name: 'sess',
    keys: ['aaa', 'bbb', 'ccc'],
    maxAge: 24*3600*1000
}));

//使用session
server.use('/', (req, res) => {
   if(!req.session.count) {
       req.session.count = 1;
   } else {
       req.session.count++;
   }
   console.log(req.session.count);
   res.send('how are you');
   res.end();
});
 ```



### session的删除
`delete req.session`