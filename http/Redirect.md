redirect - 重定向

​	301 - 永久移动，下次浏览器会根据缓存，直接访问重定向之后的路径

​	302 - 暂时移动，每次浏览器都会先访问旧路径，再被重定向到新路径



设置重定向

```js
const http = require('http');

http.createServer((req, res) => {
    if(req.url === '/') {
        res.writeHead(302, {
            "Location": "/new"  //将目标地址重定向到域内/new路径下
        });
        res.end();
    }
    if(req.url === '/new') {
        res.writeHead(200, {
            "Content-Type": "text/html",
        });
        res.end('<div>this is content</div>');
    }


}).listen(8888);
```





