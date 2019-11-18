# apply、call、bind 区别

 apply、call、bind 都是可以改变 this 的指向的，但是这三个函数稍有不同。

在 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) 中定义 apply 如下；

> apply() 方法调用一个函数, 其具有一个指定的this值，以及作为一个数组（或类似数组的对象）提供的参数

语法：

> fun.apply(thisArg, [argsArray])

- thisArg：在 fun 函数运行时指定的 this 值。需要注意的是，指定的 this 值并不一定是该函数执行时真正的 this 值，如果这个函数处于非严格模式下，则指定为 null 或 undefined 时会自动指向全局对象（浏览器中就是window对象），同时值为原始值（数字，字符串，布尔值）的 this 会指向该原始值的自动包装对象。
- argsArray：一个数组或者类数组对象，其中的数组元素将作为单独的参数传给 fun 函数。如果该参数的值为null 或 undefined，则表示不需要传入任何参数。从ECMAScript 5 开始可以使用类数组对象。浏览器兼容性请参阅本文底部内容。

## apply 和 call 的区别

其实 apply 和 call 基本类似，他们的区别只是传入的参数不同。

call 的语法为：

```
fun.call(thisArg[, arg1[, arg2[, ...]]])复制代码
```

所以 apply 和 call 的区别是 call 方法接受的是若干个参数列表，而 apply 接收的是一个包含多个参数的数组。

例 13：

```
var a ={
name : "Cherry",
fn : function (a,b) {
console.log( a + b)
}
}

var b = a.fn;
b.apply(a,[1,2])     // 3
```

例 14：

```
var a ={
name : "Cherry",
fn : function (a,b) {
console.log( a + b)
}
}

var b = a.fn;
b.call(a,1,2)       // 3
```



## bind 和 apply、call 区别

```
var a ={
name : "Cherry",
fn : function (a,b) {
console.log( a + b)
}
}

var b = a.fn;
b.bind(a,1,2)复制代码
```

我们会发现并没有输出，这是为什么呢，我们来看一下 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) 上的文档说明：

> bind()方法创建一个新的函数, 当被调用时，将其this关键字设置为提供的值，在调用新函数时，在任何提供之前提供一个给定的参数序列。

所以我们可以看出，bind 是创建一个新的函数，我们必须要手动去调用：

```
var a ={
name : "Cherry",
fn : function (a,b) {
console.log( a + b)
}
}

var b = a.fn;
b.bind(a,1,2)()           // 3
```





 https://juejin.im/post/59bfe84351882531b730bac2 