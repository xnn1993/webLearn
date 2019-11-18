## http模块使用

```js
const http = require('http');
```





## 创建一个服务

```js
//http.createServer(回调函数).listen(端口号)

http.createServer((req, res) => {
	res.write('xxx'); //返回给请求的数据
	res.end(); //表示这个请求响应完成
})
```

### req的常用属性：

- url  -  请求服务器的页面url，如`/html.123`

  ​		chrome返回的url包括请求页面的url，也包括/favicon.ico,chrome浏览器自己加的

- 