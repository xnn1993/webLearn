## 安装

`npm i webpack webpack-cli -D`



## 打包

`npx webpack` //使用webpack处理Index.js这个文件



## 总结



webpack是一个模块打包工具，可以识别出引入模块的语法，现在的webpack可以是css,png,vue的模块打包工具



## 配置

### 默认配置

文件名： `webpack.config.js`

```js
modlue.exports = {
    entry: './src/index.js', // 默认的入口文件
    output: './dist/main.js' // 默认的打包后的输出
}
```

`npx webpack` //执行命令后，webpack会找到默认的配置文件，并使用执行



### 指定配置文件

`npx webpack --config webpackconfig.js` //指定webpack使用`webpackconfig.js`文件来作为配置文件并执行

### 配置结构

```js
const path = require('path');
module.exports = {
    entry: "./src/index.js", //指定打包的入口文件
    output: {
        path: path.resolve(__dirname, "./dist"), //指定打包输出文件目录，需要配置为绝对路径
        filename: "main.js" //输出文件名称，filename: "[name].js",占位符，打包后文件名和entry中的键值一致
    },
    mode: "development", //打包环境 development(开发环境)/production(生产环境)
    module: { //模块解析处理，对于webpack不能处理的模块，指定解析工具进行处理
        rules: [
            //loader模块处理
            {
                test: /\.css$/,
                use: "style-loader"
            }
        ]
    },
    plugins: [new HtmlWebpackPlugin()] //插件配置
};
```



#### entry

指定webpack打包入口文件：webpack执行构建的第一步将从entry开始

```js
//单入口 SPA，本质是个字符串
entry: {
    main: './src/index.js'
}

===相当于===
entry: './src/index.js'

//多入口文件
entry: {
    index: './src/index.js', 
    login: './src/login.js' //指定Chunk Names 为login
}
```



#### output

打包转换后的文件输出到磁盘位置：输出结果，在webpack经过一系列处理并得出最终想要的代码后输出结果

```js
output: {
    filename: 'bundle.js', //输出文件的名称
    path: path.resolve(__dirname, "dist") //输出文件到磁盘的目录，必须是绝对路径
}

//多入口的处理
output: {
    filename: "[name]-[chunkhash:8].js", //利用占位符，文件名称不重复。占位符有[name],[hash],[chunkhash],[contenthash]。[chunkhash:8]为指定只取chunkhash的前8位
    path: path.resolve(__dirname, "dist") //输出文件到磁盘的目录，必须是绝对目录
}
```



#### mode

用来指定当前的构建环境

- production（默认值）- 帮助模块压缩，处理副作用等功能
- development - 有利于热更新的处理，识别哪个模块变化
- none

设置mode可以自动触发webpack内置的函数，达到优化的效果

| 选项        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| development | 会将`DefinePlugin`中的`process.env.NODE_ENV`的值设置为`development`。启用`NamedChunksPlugin`和`NamesModulesPlugin` |
| production  | 会将`DefinePlugin`中的`process.env.NODE_ENV`的值设置为`production`。启用`FlagIncludedChunksPlugin`,`FlagDependencyUsagePlugin`,`ModuleConcatenationPlugin`，`NoEmitOnErrorsPlugin`,`OccurrenceOrderPlugin`,`SideEffectsFlagPlugin`,`TerserPlugin` |
| none        | 退出任何默认优化选项                                         |



#### loader

模块解析，模块转换器，用于把模块原有内容按照需求转换成新内容

webpack是模块打包工具，而模块不仅仅是js，还可以是css，图片或者其他格式。但是webpack默认只知道如何处理`js`和`JSON`模块，其他格式的模块处理和处理方式需要依赖于loader处理



常见的loader

```js
style-loader
css-loader
less-loader
sass-loader
ts-loader //将ts转化为js
babel-loader //转换ES6，ES7等js新特性语法
file-loader //处理图片、文件
eslint-loader
```



loader执行顺序：从右到左，从上到下

```js
{
    test: /\.css$/,
    use: ["sttyle-loader", "css-loader", "less-loader"]
}
```





#### module

模块，在webpack中一切皆模块，一个模块对应一个文件。Webpack会从配置的`entry`开始递归找出所有依赖的模块

```js
module: {
    rules: [
        //loader模块处理
        {
            test: /\.css$/,
            use: "style-loader"
        }
    ]
}
```

当webpack处理到不认识的模块时，需要在webpack中的module处进行配置，当检测到时什么格式的模块，则使用对应的loader进行处理

- file-loader

  安装： `npm i file-loader -D`

  ```js
  module: { //模块解析处理，对于webpack不能处理的模块，指定解析工具进行处理
      rules: [
          //loader模块处理
          {
              test: /\.(jpe?g|png|gif)$/,
              //use使用一个loader可以用对象，字符串，两个loader需要用数组
              //use: 'file-loader',
              use: {
                  loader: 'file-loader',
                  options: {
                      //[占位符]，[name]老资源块的名称 [ext]老资源块的后缀
                      name: "[name]_[hash].[ext]",
                      //打包后存放的位置
                      outputPath: "images/",
                      //打包后图片的访问目录（基于打包后文件位置）
                      publicPath: "./images"
                  }
              }
  
          }
      ]
  }
  ```

- url-loader

  器内部使用了file-loader，故可以处理file-loader所有的事情，但是遇到jpg格式的模块，会把该图片转换成base64格式字符串，并打包到js中。对小体积的图片比较合适，不适用于大图片

  安装： `npm i url-loader -D`

  ```js
  module: { //模块解析处理，对于webpack不能处理的模块，指定解析工具进行处理
          rules: [
               {
                  //下面生效后，这里就不生效了
                  test: /\.(jpe?g|png|gif)$/,
                  //use使用一个loader可以用对象，字符串，两个loader需要用数组
                  //use: 'file-loader',
                  use: {
                      loader: 'file-loader',
                      options: {
                          //[占位符]，[name]老资源块的名称 [ext]老资源块的后缀
                          name: "[name]_[hash].[ext]",
                          //打包后存放的位置
                          outputPath: "images/",
                          //打包后图片的访问目录（基于打包后文件位置）
                          publicPath: "./images"
                      }
                  }
  
              }, {
                  test: /\.(jpe?g|png|gif)$/,
                  use: {
                      loader: "url-loader",
                      options: {
                          name: "[name].[ext]",
                          outputPath: "images/",
                          limit: 2028 //当图片资源小于2K时候，将图片转化为base64，直接打包到js中
                      }
                  }
              }
          ]
      }
  ```



#### 	plugins

​	plugins可以在webpack运行到某个阶段时，帮助用户自定义执行某些行为，类似于声明周期钩子，在webpack构建流程中的特定时机注入扩展逻辑来改变构建结果或者执行某些操作。作用于整个构建过程。

##### 	htmlWebpackPlugin

​	`htmlWebpackPlugin`会在打包结束后，自动生成一个html文件，并把打包生成的js模块引入到该html中

​	安装：`npm i html-webpack-plugin -D`

```js
const HtmlWebpackPlugin = require('html-webpack-plugin');

plugins: [new HtmlWebpackPlugin({
    template: "./src/index.html",
    inject: "head"
})] //插件配置
```

配置项：
![image-20191112162425770](C:\Users\xnn\AppData\Roaming\Typora\typora-user-images\image-20191112162425770.png)



##### cleanWebpackPlugin

安装 : `npm i clean-webpack-plugin -D`

在每次打包之前，清理打包后的文件目录

```js
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

plugins: [
    new CleanWebpackPlugin()
]
```



##### miniCssExtractPlugin

用来将css在打包后抽离成单独的文件

安装： `npm i mini-css-extract-plugin -D`

注意：该插件的功能和`style-loader`的功能有冲突，故而不能和`style-loader`同时配置使用

```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module: {
    rules: [
        {
            test: /\.css$/,
            use: [MiniCssExtractPlugin.loader, "css-loader"] //loader的执行顺序是从右到左
        },
    ]
}

plugins: [  //插件配置
    new MiniCssExtractPlugin({
        filename: "[name]_[contenthash:8].css"
    })
]
```







### 文件监听

轮询判断文件的最后编辑时间时候变化，某个文件发生了变化，并不会立刻告诉监听者，先缓存起来



webpack有两种监听模式

1. 启动webpack命令式

   带上`--watch`参数，启动监听后，需要手动刷新浏览器

   ```js
   //package.json
   scripts: {
       "watch": "webpack --watch"
   }
   ```

   

2. 配置文件中设置 watch: true

   ```js
   //webpack.config.js
   watch: true, //默认false，不开启
   watchOptions: {
       //默认为空，不监听的文件或者目录，支持正则
       ignored: /node_modules/,
       //监听到文件变化后，等300ms再去执行，默认300ms
       aggregateTimeout: 300,
       //判断文件是否发生变化是通过不停的询问系统指定文件是否发生变化，默认每秒访问1次，ms为单位
       poll: 1000
   }
   ```

   

### 样式处理

#### css-loader

分析css模块之间的关系，合并成一个css



#### style-loader

会将css-loader生成的内容，以style标签挂载到页面的head部分

`npm i style-loader css-loader -D`

```js
{
    test: /\.css$/,
    use: ["style-loader", "css-loader"] //loader的执行顺序是从右到左
}
```



#### less-loader

把less语法转化成css，(注意，需要安装less)

`npm i less less-loader -D`



#### Postcss-loader

样式做兼容处理，自动添加前缀

安装`npm i postcss-loader autoprefixer -D`

```js
{
    test: /\.less$/,
        use: ['style-loader',
              "css-loader",
              "less-loader",
              {
                  loader: "postcss-loader",
                  options: {
                      plugins: () => [
                          require("autoprefixer")({
                              overrideBrowserslist: ["last 2 versions", ">1%"]
                          })
                      ]
                  }
              }]
}
```

```less
body {
  background: red;
  display: flex;
}

//postcss-loader + autoprefixer 增加前缀后
body {
    background: red;
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
}
```



### Hash

- hash

  webpack构建的hash版本，构建一次变化一次

- chunkhash

  主要针对入口文件，当对应的模块发生变化时候，打包后该chunkhash发生变化，否则不发生变化

- contenthash

  主要针对css文件，当对应的css文件内容发生变化时候，该contenthash发生变化，否则不发生变化



### SourceMap

源代码与打包后的代码的映射关系，通过sourceMap定位到源码。

在`mode: development`模式下默认开启。可以在配置文件中配置关闭

```js
devtool: "none"
```

 devtool选项控制是否生成，以及如何生成 source map。 

`devtool`选项可配置值说明：

 https://webpack.docschina.org/configuration/devtool/ 

| 配置项名   | 说明                                          |
| ---------- | --------------------------------------------- |
| eval       | 速度最快，使用eval包裹模块代码                |
| source-map | 产生`.map`文件                                |
| cheap      | 较快，不包含列信息                            |
| module     | 第三方模块，包含loader的sourcemap             |
| inline     | 将`.map`作为DataURL嵌入，不单独生成`.map`文件 |

配置推荐：

```js
//开发环境配置
devtool: "cheap-module-eavl-source-map"

//线上生成配置，一般不推荐开启
devtool: "cheap-module-source-map"
```



### WebpackDevServer

提升开发效率的利器

改善的问题：每次改完代码需要重新打包一次，并且浏览器需要手动刷新

安装：`npm i webpack-dev-server -D`

#### 配置：

```js
//package.json

"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack",
    "serve": "webpack-dev-server" //配置webpackDevServer
}
```

```js
//webpack.config.js
devServer: {
    contentBase: "./dist", //服务器从哪个目录中提供内容。只有在想要提供静态文件时才需要
    open: true,  //构建之后，自动打开浏览器
    port: 8081  // 指定端口号，默认为8080
}
```

启动服务后，dist目录没有了，这是因为devServer不会把打包之后的模块放在dist目录下，而是放到内存中，从而提升速度



#### 跨域

联调期间，前后端分离，直接获取数据会跨域，上线后用nginx转发，开发期间则可使用webpack处理

启动一个服务器，mock一个接口

```js
// npm i express -D
// 创建一个server.js 修改scripts: {"server": "node server.js"}

//server.js
const express = require("express")
const app = express();
app.get('/api/info', (req, res) => {
    res.json({
        name: 'xnn',
        age: '18',
        msg: '一切都是最好的安排'
    })
}).listen(9999)
```

安装axios

```js
//npm i axios -D
//index.js
import axios from "axios"
axios.get('http://localhost:9999/api/info')
	.then(res => {
        console.log(res);
    })
```

上述有跨域的问题，报错如下：

![image-20191114213137580](C:\Users\xnn\AppData\Roaming\Typora\typora-user-images\image-20191114213137580.png)



解决方式：

- 修改`webpack.config.js`设置服务器代理

```js
devServer: {
    contentBase: './dist',
    open: true,
    port: 8888,
    proxy: {
    	"/api": {
        	target: "http://localhost:9999"
        }
    }
},
```

- 修改`index.js`

```js
axios.get("/api/info")
    .then(res => {
    	console.log(res);
})
```

