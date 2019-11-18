## 介绍

Flex 是 Flexible Box 的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。

任何一个容器都可以指定为 Flex 布局。

```css
.box{
    display: flex;
}
```

行内元素也可以使用 Flex 布局。

 ```css
.box{
    display: inline-flex;
}
 ```

Webkit 内核的浏览器，必须加上`-webkit`前缀。

 ```css
.box{
    display: -webkit-flex; /* Safari */
    display: flex;
}
 ```

注意，设为 Flex 布局以后，子元素的`float`、`clear`和`vertical-align`属性将失效。



## 基本概念

 采用 Flex 布局的元素，称为 Flex 容器（flex container），简称"容器"。它的所有子元素自动成为容器成员，称为 Flex 项目（flex item），简称"项目"。 

容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做`main start`，结束位置叫做`main end`；交叉轴的开始位置叫做`cross start`，结束位置叫做`cross end`。

项目默认沿主轴排列。单个项目占据的主轴空间叫做`main size`，占据的交叉轴空间叫做`cross size`。

![image-20191106221449048](C:\Users\xnn\AppData\Roaming\Typora\typora-user-images\image-20191106221449048.png)



## 容器的属性
```
flex-direction
flex-wrap
flex-flow
justify-content
align-items
align-content
```

### flex-direction

`flex-direction`属性决定主轴的方向

```css
.box {
    flex-direction: row | row-reverse | column | column-reverse
}
```
- row(默认值)：主轴为水平方向，起点在左端
- row-reverse: 主轴为水平方向，起点在右端
- column: 主轴为垂直方向，起点在上部
- column-reverse: 主轴为垂直方向，起点在下部

![image-20191106222849004](C:\Users\xnn\AppData\Roaming\Typora\typora-user-images\image-20191106222849004.png)



### flex-wrap

`flex-wrap`属性定义 当一条轴线排放不下后的换行问题

```css
.box{
  flex-wrap: nowrap | wrap | wrap-reverse;
}
```

- nowrap(默认值): 不换行
- wrap: 换行，第一行在上方
- wrap-reverse: 换行，第一行在下方

![image-20191106224146367](C:\Users\xnn\AppData\Roaming\Typora\typora-user-images\image-20191106224146367.png)



### flex-flow

`flex-flow`属性是`flex-direction`和`flex-wrap`的简写，默认值为 `row nowrap`

```css
.box {
    flex-flow: <flex-direction> <flex-wrap>;
}
```



### justify-content

`justify-content`属性定义了项目在主轴上的对齐方式。

```css
.box{
    justify-content: flex-start | flex-end | center | space-between | space-around;
}
```

- flex-start(默认): 左对齐
- flex-end: 右对齐
- center: 居中
- space-between: 两端对齐，项目之间的间隔相等
- space-around: 每个项目两侧的间隔相等。（项目之间的间隔比项目与边框的间隔大一倍）

![image-20191107104059651](C:\Users\xnn\AppData\Roaming\Typora\typora-user-images\image-20191107104059651.png)



### align-items

`align-items`定义项目在交叉轴上的对齐方式

```css
.box {
    align-items: flex-start | flex-end | center | baseline | stretch
}
```

- flex-start: 交叉轴的起点对齐
- flex-end: 交叉轴的终点对齐
- center: 交叉轴的中点对齐
- baseline: 项目的第一行文字的基线对齐
- stretch(默认值): 如果项目未设置高度或者设置为`auto`，将占满整个容器的高度

![image-20191107105804732](C:\Users\xnn\AppData\Roaming\Typora\typora-user-images\image-20191107105804732.png)



### align-content

`align-content`属性定义了多根轴线的对齐方式。如果项目只有一根轴线，则该属性不起作用

```css
.box {
    align-content: flex-start | flex-end | center | space-between | space-around | stretch;
}
```

- flex-start: 与交叉轴的起点对齐
- flex-end: 与交叉轴的终点对齐
- center: 与交叉轴的中点对齐
- space-between: 与交叉轴的两端对齐，轴线之间的间隔平均分布
- space-around: 每根轴线两侧的间隔都相等。（轴线之间的间隔是轴线与边框的间隔的两倍）
- stretch(默认值): 轴线占满整个交叉轴，注意，不能设置单个项目的高度

![image-20191107111630999](C:\Users\xnn\AppData\Roaming\Typora\typora-user-images\image-20191107111630999.png)



## 项目的属性
```
order
flex-grow
flex-shrink
flex-basis
flex
align-self
```

### order
`order`属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。

```css
.item {
    order: <integer>
}
```








### flex-grow


### flex-shrink


### flex-basis


### flex


### align-self