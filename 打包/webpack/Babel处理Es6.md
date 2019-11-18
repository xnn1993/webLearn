## 安装

`npm i babel-loader @babel/core @babel/preset-env -D`

`babel-loader`是`webpack` 与 `babel` 的通信桥梁，不会将es6转化为es5

`@babel/preset-env` 来将es6转化成es5，其中包含了es6到es5的转化规则

```js
//webpack.config.js
module: {
    rules: [
        {
            test: /\.js$/,
            use: ['babel-loader']
        }
    ]
}
```

```js
//index.js
const arr = [new Promise(()=>{}), new Promise(()=>{})];
arr.map(item=>{
    console.log(item);
})
```

通过上述的步骤，不能将Promise等转化过来，这时候需要借助`@babel/polyfill`，将es的新特性装进来，来弥补低版本浏览器中缺失的特性



## @babel/polyfill

polyfill以全局的方式注入进来的，注入到全部变量window下，会造成全局对象的污染。

故而，不适用于开发组件库和工具库



安装：`npm i @babel/polyfill -S`

```js
//webpack.config.js
{
    test: /\.js$/,
    exclude: /node_modules/,
    use: {
      	loader: 'babel-loader',
    	options: {
        	presets: ['@babel/preset-env']
    	}  
    }
    
}

//index.js
import "@babel/polyfill";
```

上述代码会导致在`index.js`中polyfill会将将es6、es7等所有的新特性都注入进来，使得打包后的index.js体会相当大。如何只注入当前代码需要的特性，从而减小打包体积呢？

```js
//webpack.consig.js
{
    test: /\.js$/,
	exclude: /node_modules/,
    use: {
    	loader: 'babel-loader',
        options: {
        	presets: [
            	['@babel/preset-env', {
                	targets: {
                    	edge: '17',
                        firefox: '60',
                        chrome: '67',
                        safari: '11.1'
                    },
                    useBuiltIns: 'usage' //按需注入
                 }]
        	]
       	}
   	}
}

//此时不需要在index.js中手动引入@bable/polyfill
```

`useBuiltIns`选项是`babel 7`的新功能，这个选项用来告诉babel如何配置@babel/polyfill。

其可配置的参数如下

| 参数值 | 说明                                                         | 推荐指数 |
| ------ | ------------------------------------------------------------ | -------- |
| entry  | 需要在`webpack`的入口文件中`import "@babel/polyfill"`一次。babel会根据代码使用情况导入相应的垫片 | 推荐     |
| usage  | 不需要`import`，全自动检测，按需引入                         | 试验阶段 |
| false  | 如果`import "@babel/polyfill"`,将会注入所有特性，代码体积会很庞大 | 不推荐   |



## .babelrc文件

将webpack.config.js中对应的options选项的内容移动至该文件即可，可使得webpack.config.js文件不至于过长

```js
//.babelrc
{
  "presets": [
    ["@babel/preset-env", {
      "targets": {
        "edge": "17",
        "firefox": "60",
        "chrome": "67",
        "safari": "11.1"
      },
      "useBuiltIns": "usage",
      "corejs": 2 //指定core（核心库版本），消除警告
    }]
  ]
}

//webpack.config.js
{
    test: /\.js$/,
   	exclude: /node_modules/,
    use: "bable-loader"
}
```

 

## @babel/plugin-transform-runtime

采用闭包方式注入，不会污染全局环境，和polyfill使用场景相反，作用一致

安装：

```js
npm i @babel/plugin-transform-runtime ---save-dev
npm i @babel/runtime -S
```

使用：

```js
//.babelrc 或者 webpack.config.js中配置均可
"plugins": [
    [
        "@babel/plugin-transform-runtime",
        {
            "absoluteRuntime": false,
            "corejs": false,
            "helpers": true,
            "regenerator": true,
            "useESModules": false
        }
    ]
]
```



## tree Shaking（摇树）

用于移除js上下文中的未引用代码

注意：只支持ES modules的引入方式

```js
//webpack.config.js
optimization: {
    usedExports: true  //开启tree Shaking
}

//package.json
"sideEffects": false //正常对所有模块进行tree Shaking
// "sideEffects": ["*.css", '@babel/polyfill'] //对除css文件和@babel/polyfill之外的模块进行tree Shahking
```

案例：

```js
//expo.js
export const add = (a, b) => {
    console.log(a + b);
}

export const minus = (a, b) => {
    console.log(a-b);
}

//index.js
import {add} from "./expo";
add(1,2);
```

因为没有用到`expo.js`中的`minus`，故而在生产环境下打包后的文件中未将minus打入包中



##  code splitting（代码分割）

问题：业务逻辑中引入第三方公共库，导致1.打包后文件体积大；2.业务逻辑多变化，第三方工具不变化，导致只要业务逻辑发生变化，第三方公共库也要重新进行打包，发生变化



解决方式：使用code Splitting将公共库代码单独打包，和业务代码打包分离

案例：

```js
//loadsh.js
import _ from "lodash";
window._ = _;

//index.js(因为lodash挂在了window上，故而不需要引入了)
console.log(_.join(['a', 'b', '****']))

//webpack.config.js 
optimization: {
    //自动做代码分割
    splitChunks: {
        chunks: "all" //默认支持异步
    }
}
```

`splitChunks`的配置项说明

```js
  optimization: {
    splitChunks: {
      chunks: 'async', //initial(自动分割同步代码)/async(自动分割异步代码)/all(所有模块有效)
      minSize: 30000,  //设置需要分割模块的最小尺寸
      maxSize: 0, //模块进行二次分割，不推荐使用
      minChunks: 1, //打包生成的chunk文件要求最少有几个chunk引用了这个模块
      maxAsyncRequests: 5, //最大异步请求数,默认5
      maxInitialRequests: 3, //最大初始化请求数，入口文件同步请求，默认3
      automaticNameDelimiter: '~', //打包分割符号
      name: true, //打包后的名称，除了bool值，还可以接受一个function
      cacheGroups: { //缓存组
        vendors: {
          test: /[\\/]node_modules[\\/]/,
          priority: -10 //缓存组优先级（数字越大，优先级越高）
        },
        default: {
          minChunks: 2,
          priority: -20,
          reuseExistingChunk: true
        }
      }
    }
  }
```



### 预取/预加载模块[prefetch/preload module]

在声明 import 时，使用prefetch 和preload内置指令，可以让 webpack 输出 "resource hint(资源提示)"，来告知浏览器： 

- prefetch(预取)：将来某些导航下可能需要的资源

- preload(预加载)：当前导航下可能需要的资源 -- 推荐

```js
//使用prefetch，按需加载LoginModal组件
import(/* webpackPrefetch: true */ 'LoginModal');

//使用preload，立即按需导入ChartingLibrary组件
import(/* webpackPreload: true */ 'ChartingLibrary');
```



与 prefetch 指令相比，preload 指令有许多不同之处：

- preload chunk 会在父 chunk 加载时，以并行方式开始加载。prefetch chunk 会在父 chunk 加载结束后开始加载。
- preload chunk 具有中等优先级，并立即下载。prefetch chunk 在浏览器闲置时下载。
- preload chunk 会在父 chunk 中立即请求，用于当下时刻。prefetch chunk 会用于未来的某个时刻。
- 浏览器支持程度不同。