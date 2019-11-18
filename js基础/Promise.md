## Constructor

创建`promise`对象,可以使用`new`来调用`promise`的构造器来进行实例化

```js
var promise = new Promise((resolve, reject) => {
    //异步处理
    //处理结束后调用resolve 或者 reject
})
```



## Instance Method

- resolve

  `onFulfilled`会被调用

- reject

  `onRejected`会被调用

- promise.then

  `promise.then(onFulfilled, onRejected)`

  成功和失败时都可以调用。

  若指向对异常进行处理，可采用`promise.then(undefined, onRejected)`这种方式，只指定reject时的回调函数即可。

- promise.catch(onRejeced)

  只处理onRejected（失败）时的情况



```js
function getURL(URL) {
    return new Promise(function (resolve, reject) {
        var req = new XMLHttpRequest();
        req.open('GET', URL, true);
        req.onload = function () {
            if (req.status === 200) {
                resolve(req.responseText);
            } else {
                reject(new Error(req.statusText));
            }
        };
        req.onerror = function () {
            reject(new Error(req.statusText));
        };
        req.send();
    });
}


// 运行示例
var URL = "http://httpbin.org/get";
getURL(URL).then(function onFulfilled(value){
    console.log(value);
}).catch(function onRejected(error){
    console.error(error);
});
```





## Static Method

- Promise.all

  接收一个 promise对象的数组作为参数，当这个数组里的所有promise对象全部变为resolve或reject状态的时候，它才会去调用 `.then` 方法。 

  ```js
  function getURL(URL) {
      return new Promise(function (resolve, reject) {
          var req = new XMLHttpRequest();
          req.open('GET', URL, true);
          req.onload = function () {
              if (req.status === 200) {
                  resolve(req.responseText);
              } else {
                  reject(new Error(req.statusText));
              }
          };
          req.onerror = function () {
              reject(new Error(req.statusText));
          };
          req.send();
      });
  }
  
  var request = {
          comment: function getComment() {
              return getURL('http://azu.github.io/promises-book/json/comment.json').then(JSON.parse);
          },
          people: function getPeople() {
              return getURL('http://azu.github.io/promises-book/json/people.json').then(JSON.parse);
          }
      };
  function main() {
      return Promise.all([request.comment(), request.people()]);
  }
  
  // 运行示例
  main().then(function (value) {
      console.log(value);
  }).catch(function(error){
      console.log(error);
  });
  ```

   在上面的代码中，`request.comment()` 和 `request.people()` 会同时开始执行，而且每个promise的结果（resolve或reject时传递的参数值），和传递给 `Promise.all` 的promise数组的顺序是一致的。 



- Promise.race

  只要有一个promise对象进入 FulFilled 或者 Rejected 状态的话，就会继续进行后面的处理 

  ```js
  // `delay`毫秒后执行resolve
  function timerPromisefy(delay) {
      return new Promise(function (resolve) {
          setTimeout(function () {
              resolve(delay);
          }, delay);
      });
  }
  
  // 任何一个promise变为 resolve 或 reject 的话程序就停止运行
  Promise.race([
      timerPromisefy(1),
      timerPromisefy(32),
      timerPromisefy(64),
      timerPromisefy(128)
  ]).then(function (value) {
      console.log(value);    // => 1
  });
  ```

    `Promise.race`在第一个promise对象变为Fulfilled之后，并不会取消其他promise对象的执行 



- Promise.resolve

  可快速常见一个promise实例，返回值也是一个promise对象

  ```js
  new Promise(function(resolve){
      resolve(42);
  });
  
  //上述代码等价于如下
  Promise.resolve(42);
  ```

  

- Promise.reject

  同`Promise.resolve`

  ```js
  new Promise(function(resolve,reject){
      reject(new Error("出错了"));
  });
  
  //上述代码相当于如下
  promise.reject(new Error('出错了'));
  ```

  



## Promise的状态

- Fulfilled

  resolve(成功)时，此时会调用`onFulfilled`

- Rejected

  reject(失败)时，此时会调用`onRejected`

- Pending

  既不是resolve也不是reject的状态。是promise对象刚被创建后的初始化状态等 

![image-20191029162404654](C:\Users\xnn\AppData\Roaming\Typora\typora-user-images\image-20191029162404654.png)