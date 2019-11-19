## 作用

- 限制资源的获取

- 报告资源获取越权



## 限制方式

- default-src ： 限制全局和连接请求相关的内容范围
- 制定资源类型



### 资源类型

- connect-src
- manifest-src
- img-src
- font-src
- media-src
- style-src
- frame-src
- script-src



```js
//server.js

const http = require('http');
const fs = require('fs');

http.createServer((req, res) => {
    let content = fs.readFileSync('./test.html');
    res.writeHead(200, {
        "Content-Type": "text/html",
        "Content-Security-Policy": "default-src http: https:" //设置default-src只能通过http或者https方式来加载
        //"Content-Security-Policy": "default-src \'self'\" //显示外链只能使用本站提供的js代码
    });
    res.end(content);

}).listen(8888);
```

```html
<!DOCTYPE html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
        <title>标题</title>
    </head>
    <body>
        <div>This is content</div>
        <script>
            console.log('inline js');
        </script>
    </body>
</html>
```

![image-20191119115402110](C:\Users\xnn\AppData\Roaming\Typora\typora-user-images\image-20191119115402110.png)

因为在server.js中设置default-src为只能通过http或者https的方式获取连接进行代码执行，而html中的执行代码为内联代码，不符合限制的方式，故而报错

