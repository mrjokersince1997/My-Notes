# HTML

定义页面元素。浏览器加载 html 文件后，会将其解析为树形结构 DOM ：每个 html 标签和属性都作为一个 DOM 树节点。在全部代码（包含引入文件）解析完成后，将 DOM 树绘制并渲染为用户可见的页面。

---

## html 页面根标签

```html
<!DOCTYPE html>
<html>
    <!--your code-->
</html>
```

---

## head 页面头部

### title 页面标题

```html
<title>页面标题</title>
``` 

### meta 页面元数据

*页面作者 / 页面描述*

```html
<meta name="author" content="MrJoker">
<meta name="description" content="这是你的页面。">
```

*页面类型 / 页面编码*

```html
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
```

- http-equiv：元数据在 response 头部返回浏览器

### link 外部资源链接

*引入 网页图标 / CSS*

```html
<link href="/favicon.ico" rel="icon" type="image/x-icon" />
<link href="asserts/css/bootstrap.min.css" rel="stylesheet" type="text/css" />
```


### style 页面样式

```html
<style type="text/css">
    body {background-color:yellow}
    p {color:blue}
</style>
```

### script 执行脚本

- 引入外部脚本文件

```html
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js">
```

- 自定义脚本

```html
<script type="text/javascript">
  document.getElementById("demo").innerHTML = "My first JavaScript";
</script>
```

## 页面内容 body

### div / span 区域 

- `<div>` 为块状元素：独占一行
- `<span>` 为内联元素：和其他内联元素共享一行


### h1 - h6 标题

`<h1>Chapter 1</h1>`

### p 文本

`<p>This is my first paragraph.</p>`

### a 超链接

`<p href="www.baidu.com">This is my second paragraph.</p>`

- href：跳转网址

### img 图片

`<img src="boat.gif" alt="Big Boat" />`

- src：图片地址
- alt：图片不能加载时的替代文本

### table 表格

- 每个单元项表示为`<tr>`
- 每个单元格表示为`<td>` ，标题单元格表示为`<th>`

```html
<table>
  <tr>
    <th>学号</th>
    <th>姓名</th>
  </tr>
  <tr>
    <td>0021</td>
    <td>陈柏言</td>
  </tr>
  <tr>
    <td>0022</td>
    <td>邓怀瑾</td>
  </tr>
</table>
```

### form 表单

```html
<form action="action_page.php" method="GET">
  Name:
  <input type="text" name="name" value="Input your nane">
  <br/>
  Sex:
  <input type="radio" name="sex" value="male" checked>Male
  <input type="radio" name="sex" value="female">Female
  <br/>
  <input type="submit" value="Submit">
</form> 
```

- action：请求发送地址(URL)
- method：请求类型

**提交按钮**

`<input type="submit" value="Submit">`

**输入类型**

- 输入框

```html
Username:
<input type="text" name="name" value="MrJoker">
<br/>
Password:
<input type="password" name="password">  
```

- 选择框

```html
Sex:
<input type="radio" name="sex" value="male" checked>Male
<input type="radio" name="sex" value="female">Female
<br/>
Vehicle:
<input type="checkbox" name="vehicle" value="Bike">I have a bike
<input type="checkbox" name="vehicle" value="Car">I have a car
```

- 下拉菜单

```html
Your Car:
<select name="cars">
  <option value="volvo">Volvo</option>
  <option value="saab">Saab</option>
  <option value="fiat">Fiat</option>
  <option value="audi">Audi</option>
</select>
```

- 文本域

```html
<textarea name="message" rows="10" cols="30">Descript your car here.</textarea>
```
