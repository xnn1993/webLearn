```js
// 构造函数:
function myFunction(arg1, arg2) {
    this.firstName = arg1;
    this.lastName  = arg2;
}

// This    creates a new object
var a = new myFunction("Li","Cherry");
a.lastName;    // 返回 "Cherry"
```



new的过程：

1. 创建一个空对象obj
2. 将新创建的空对象的隐式原型指向器构造函数的显示原型
3. 用call改变this指向到该空对象obj上
4. 如果无返回值或者返回一个非对象值，则返回obj，否则返回该对象



伪代码展示new的过程

```js
var a = new myFunction("Li","Cherry");

new myFunction {
    var obj = {};
    obj.__proto__ = myFunction.prototype;
    var result = myFunction.call(obj, "Li","Cherry");
    return typeof result === 'obj' ? result : obj;
}
```

