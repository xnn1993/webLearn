## 定义和用法

`encodeURIComponent()` 函数对URI进行**编码**

`decodeURIComponent() `函数对 encodeURIComponent() 函数编码的 URI 进行**解码** 



### 语法

```
decodeURIComponent(URIstring)
```

| 参数      | 描述                                                    |
| :-------- | :------------------------------------------------------ |
| URIstring | 必需。一个字符串，含有编码 URI 组件或其他要解码的文本。 |

### 返回值

URIstring 的副本，其中的十六进制转义序列将被它们表示的字符替换。

## 实例

```html
<script type="text/javascript">

var test1="http://www.w3school.com.cn/My first/"

document.write(encodeURIComponent(test1)+ "<br />")
document.write(decodeURIComponent(test1))

</script>
```

输出：

```txt
http%3A%2F%2Fwww.w3school.com.cn%2FMy%20first%2F

http://www.w3school.com.cn/My first/
```