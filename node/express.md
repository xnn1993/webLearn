## 说明：

express是一个WEB开发框架

- 保留了原生的功能
- 在此基础上进行增强改进



## 安装：

 ` npm install express --save `



## 使用：

1. 创建服务 `var server = express();`
2. 监听端口 `server.listen(8888);`
3. 处理用户请求 `server.use(请求地址, (req, res) => {})`

```js
const express = require('express');

var server = express();

server.use('/index.html', (req, res) => {
    res.send('aaa');
    res.end();
});

/*当用户请求根目录时候或者请求链接没有匹配时候，触发该回调*/
server.use('/', (req, res) => {
    res.send('abc');
    res.end();
});

server.listen(8888);
```



server.use(path, (req, res) => {})中，req与res解析

req与res保留原始http中的req和res中的所有东西，并在其基础上添加了部分接口

- res.send()  -- res.write()仅能发送String和Buffer类型的数据，res.send()支持发送Object等各种类型的数据

- req.query  -- 对应url.parse(req.url, true).query,可以非常便捷的获取到get请求所传递的参数

- req.body -- 对应querystring.parse(str),获取post请求所传递的参数，使用之前需要`body-parser中间件`用`server.use(bodyParser.urlencoded({}))`解析post请求,并将解析的数据方式req.body中。

  ```js
  bodyParser.urlencoded({
  	extended: true/false,  //扩展模式/普通模式
  	limit: 100*1024  //限制post数据大小，默认100k
  })
  ```





## 接收用户的请求的方式

- .get - 接收get请求  `server.get(path, (req, res) => {})`
- .post - 接收post请求  `server.post(path, (req, res) => {})`
- .use - 通用，一般写到最后  `server.use(path, (req, res) => {})`





## express-static中间件

用来处理静态文件

```js
const expressStatic = require('express-static')

//若请求的文件地址在指定的./www下存在，则直接读取并返回./www下所请求的文件
server.use(expressStatic('./www'));
```





## body-parser中间件

用于对post所传数据进行解析 

```js
const express = require('express');
const bodyParser = require('body-parser');

var server = express();
server.listen(8888);

server.use(bodyParser.urlencoded({}));
server.post('/user', (req, res) => {
    console.log(req.body);
    res.send(req.body);
    res.end();
});
```



## 链式操作

回调函数中使用next启动链式操作。

```js
//没有使用next操作，所以控制台只会输出'a'
const express = require('express');

var server = express();
server.listen(9998);

server.use('/', (req, res) => {
   console.log('a');
});
server.use('/', (req, res) => {
    console.log('b');
});
```

```js
//回调中使用next操作，控制台先打印'a',再打印'b'
const express = require('express');

var server = express(); 
server.listen(9998);

server.use('/', (req, res, next) => {
   console.log('a');
   next();
});
server.use('/', (req, res) => {
    console.log('b');
});
```

