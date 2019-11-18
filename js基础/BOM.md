## 获取浏览器滚动条滚出距离（可视区与页面顶部的距离）

- 有兼容性问题

- IE和火狐上使用如下：

  `document.documentElement.scrollTop`

- chrome下使用如下：

  `document.body.scrollTop`
  
- 综合使用：

  `let scrollTop = document.documentElement.scrollTop || document.body.scrollTop`



## 事件对象的获取

- 有兼容性
- IE下可直接获取event全局事件对象
- 火狐下通过参数传入
- 综合使用

```js
document.onclick = function (ev) {
    ev = ev || event
}
```



## clientX与clientY

`clientX`与`clientY`是鼠标点击的位置距离当前浏览器可视窗口（可视区）的相对左距离和顶部距离

一般页面布局，需要`clientX + scrollLeft` 与 `clientY + scrollTop`配合使用，来获取当前页面的位置

