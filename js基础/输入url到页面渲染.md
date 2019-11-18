 

从url输入到页面渲染完成，大致可分为两个阶段：网络通信和页面渲染

1. 输入url地址
2. 浏览器根据域名查找IP地址
3. 浏览器发送http请求
4. 服务器接收数据
5. 服务器响应请求
6. 服务器返回对应的文件给客户端
7. 解析HTML文件
8. 解析CSS文件
9. js脚本解析执行
10. 生成渲染树
11. 进行页面布局
12. 进行页面绘制



![image-20191030105940674](C:\Users\xnn\AppData\Roaming\Typora\typora-user-images\image-20191030105940674.png)





## 渲染树的形成

### HTML解析器

​	从后端返回给浏览器渲染引擎 HTML 文件字节流，要经过渲染引擎中的 HTML 解析器。

​	它实现了将 HTML 字节流转换为 DOM树 结构。 HTML 文件字节流返回的过程中 HTML 解析器就一直在解析，边加载边解析。

![image-20191030110740432](C:\Users\xnn\AppData\Roaming\Typora\typora-user-images\image-20191030110740432.png)

- 字节流转化为字符并通过W3C标准令牌化
- 通过分词器将字节流转化为Token
- 将Token转为DOM节点，并将DOM节点添加到DOM树



### CSS解析器

 CSS 解析器最终的目的是构建CSSOM树，树的构建流程和 DOM 树的构建流程基本相同 。

​	为页面上的任何对象计算最后一组样式时，浏览器都会先从适用于该节点的最通用规则开始（例如，如果该节点是 body 元素的子项，则应用所有 body 样式），然后通过应用更具体的规则（即规则“向下级联”）以递归方式优化计算的样式。 

- CSS解析可以与DOM解析同时进行

- 如果只有 CSS 和 HTML 的页面，CSS 不会影响 DOM 树的创建，但是如果页面中还有 JavaScript，结论就不一样了。



### JS对DOM树及CSSOM树的影响

1.  当前页面中只有 Html 和 JavaScript，而且 JavaScript 非外部引入 

   ​	DOM 树构建时当遇到JavaScript脚本，就要暂停 DOM 解析，先去执行Javascript，因为JavaScript可能会操作当前已经生成的DOM节点。 

2.  当页面中同时有Html  JavaScript  CSS ，而且都非外部引入 

   ​	DOM 树构建时当遇到 JavaScript 脚本，就要暂停 DOM 解析，先执行 JavaScript，同时 JavaScript 还要判断 CSSOM 是否解析完成，因为在 JavaScript 可能会操作 CSSOM 节点。

   ​	CSSOM 节点确认解析完成，执行 JavaScript 再次回到 DOM 树创建。

3.  当页面中同时有Html，JavaScript， CSS ，而且外部引入 

   ​	当渲染进程接收HTML文件字节流时，会先开启一个预解析线程来提前下载JavaScript和css文件数据。DOM树在创建过程中如果遇到JavaScript文件，接下来就和情况2类型一样了。

   ![image-20191030111602945](C:\Users\xnn\AppData\Roaming\Typora\typora-user-images\image-20191030111602945.png)

   

总结：优先CSS解析  => JS执行 => HTML解析DOM



### 构建渲染树

浏览器可根据 DOM 树和 CSSOM 树来构建渲染树。浏览器会先从 DOM 树的根节点开始遍历每个可见节点，然后对每个可见节点找到适配的CSS样式规则并应用。有以下几点注意点：

- Render Tree和DOM Tree不完全对应。
- 请注意`visibility: hidden`与 `display: none` 是不一样的。前者隐藏元素，但元素仍占据着布局空间（即将其渲染成一个空框），而后者 (display: none) 将元素从渲染树中完全移除，元素既不可见，也不是布局的组成部分

### 渲染树形成过程可以做哪些优化

- 在引入顺序上，CSS 资源先于 JavaScript 资源。样式文件应当在 head 标签中，而脚本文件在 body 结束前，这样可以防止阻塞的方式。

- 尽量减少在 JavaScript 中进行DOM操作。

- 简化并优化CSS选择器，尽量将嵌套层减少到最小。

- 修改元素样式时，更改其class属性是性能最高的方法。







 https://juejin.im/post/5da2f4f7f265da5b8a516502 