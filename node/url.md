## 引入

```js
const url = require('url');
```



解析url，不解析参数部分(query不解析)

```js
var json = url.parse('http://www.xnn.com/index.html/?user=xnn&passwd=666');
console.log(json);

/*
{
  protocol: 'http:',
  slashes: true,
  auth: null,
  host: 'www.xnn.com',
  port: null,
  hostname: 'www.xnn.com',
  hash: null,
  search: '?user=xnn&passwd=666',
  query: 'user=xnn&passwd=666',
  pathname: '/index.html/',
  path: '/index.html/?user=xnn&passwd=666',
  href: 'http://www.xnn.com/index.html/?user=xnn&passwd=666'
}
*/
```



解析url，并且解析参数部分（query部分解析成一个json）,parse方法加第二个参数true

```js
var json = url.parse('http://www.xnn.com/index.html/?user=xnn&passwd=666', true);
console.log(json);

/*
{
  protocol: 'http:',
  slashes: true,
  auth: null,
  host: 'www.xnn.com',
  port: null,
  hostname: 'www.xnn.com',
  hash: null,
  search: '?user=xnn&passwd=666',
  query: { user: 'xnn', passwd: '666' },
  pathname: '/index.html/',
  path: '/index.html/?user=xnn&passwd=666',
  href: 'http://www.xnn.com/index.html/?user=xnn&passwd=666'
}
*/
```

