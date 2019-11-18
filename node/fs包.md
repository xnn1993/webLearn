## fs模块使用

```js
const fs = require('fs');
```



## 常用方法

### feadFile

异步方式读取文件

```js
//fs.readFile(file, callback);

fs.readFile('/etc/passwd', (err, data) => {
  if (err) throw err;
  console.log(data);
});
```



### writeFile

异步方式写入文件

```js
//fs.writeFile(file, data, callback);

fs.writeFile('message.txt', 'hello nodejs', (err) => {
  if (err) throw err;
  console.log('The file has been saved!');
});
```



### readFileSync

同步方式读取文件,返回读取的数据内容

```js
//fs.readFileSync(path[, options])

fs.readFileSync('<directory>');
```



### writeFileSync

同步方式写入文件，返回`undefined`

```js
//fs.writeFileSync(file, data[, options])

fs.writeFileSync('message.txt', 'hello nodejs')
```





## 用例一

```js
const http = require('http');
const fs = require('fs');

var httpServer = http.createServer((req, res) => {
    fs.readFile(`./www${req.url}`, (err, data) => {
        if (err) {
            res.write('获取文件失败，404')
        } else {
            res.write(data);
        }
        res.end();
    });
});

httpServer.listen(8050);
```

