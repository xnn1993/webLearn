## Array.prototype.slice.call()

```js
function sum(){
    let args = Array.prototype.slice.call(arguments);
    console.log(args.reduce((sum, cur) => sum + cur));
}

sum(1,2);
sum(1,2,3);
```



## Array.from()

```js
function sum(){
    let args = Array.from(arguments);
    console.log(args.reduce((sum, cur) => sum + cur));
}

sum(1,2);
sum(1,2,3);
```



## ES6展开运算符

```js
function sum(){
    let args = [...arguments];
    console.log(args.reduce((sum, cur) => sum + cur));
}

sum(1,2);
sum(1,2,3);
```



## 利用 concat + apply

```js
function sum(){
    let args = Array.prototype.concat.apply([], arguments);
    console.log(args.reduce((sum, cur) => sum + cur));
}

sum(1,2);
sum(1,2,3);
```

