forEach中的return只能中断某次循环，继续下次循环，类似continue；无法中断循环。

```js
let nums = [1,2,3];
let res = [];
nums.forEach((num, index) => {
    if(num === 2) {
        console.log('aaa'); // aaa
        return;
    }
    res.push(num);
});
console.log(res); //[1,3]
```



中断循环方法：

1. 用tryJ电视代码块，在需要中断的地方抛出异常

   ```js
   let nums = [1,2,3];
   let res = [];
   try {
       nums.forEach((num, index) => {
           if(num === 2) {
               console.log('aaa'); // aaa
               throw new Error();
           }
           res.push(num);
       });
   } catch(e){
       
   }
   
   console.log(res); //[1]
   ```

   

2. 用every和some替代forEach函数，every在碰到return false时候，终止循环；some在碰到 return true时候，终止循环

   ```js
   let nums = [1,2,3];
   let res = [];
   
   nums.every((num, index) => {
       if(num === 2) {
           return false;
       }
       res.push(num);
       return true;
   });
   
   console.log(res); //[1]
   ```

   ```js
   let nums = [1,2,3];
   let res = [];
   
   nums.some((num, index) => {
       if(num === 2) {
           return true;
       }
       res.push(num);
       return false;
   });
   
   console.log(res); //[1]
   ```

   

