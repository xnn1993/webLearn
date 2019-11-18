包的引入及使用

```js
const queryString = require('querystring');

var json = queryString.parse('user=aaa&passwd=kjasdhf');
console.log(json);
/* 
 {   
	user: 'aaa',   
	passwd: 'kjasdhf'
 }
*/
```

