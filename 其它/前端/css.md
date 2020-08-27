# CSS

定义页面样式。浏览器在构造完成页面的 DOM 树后，会解析 css 代码以及引入的 css 文件，将定义的样式表规则添加到对应的 DOM 树节点上。在绘制并渲染为用户可见的页面时使用。



---

## 选择器

选择器确定 CSS 样式的作用范围。

```html
<div>
  <h1>title</h1>
  <p class="px" id="p1">first paragraph</p>
  <p class="px" id="p2">second paragraph</p>
  <a type="button" href="./next.html">next</a>
</div>

<style>
div { letter-spacing:0.5em; }               /* 简单选择器 */
.px { font-size:20px; }                     /* 类选择器 */ 
#p1 { color:brown; }                        /* ID 选择器 */ 
[type="button"] { display:block; }          /* 属性选择器 */             
</style>
```

**复杂类选择器**


```html
<p class="important welcome">Hello</p>

<div class="warning">
  <h1>NO</h1>
  <p class="text">STOP</p>
</div>

<style>
  /* 多重条件触发 */
  .important.welcome { background:silver; }
  /* 嵌套条件触发*/
  div h1 { font-weight:bold; }    
  .warning .text { background:silver; }
</style>
```

---

## 定位方式

1. 静态定位(static)：默认情况，元素从上到下依次放入 html 页面。
2. 相对定位(relative)：根据其正常位置偏移。
3. 绝对定位(absolute)：根据首个非静态父元素位置偏移。
4. 固定定位(fixed)：相对于浏览器窗口偏移，即使滚动窗口也不发生改变。

- `position:relative;` 设置元素为相对定位
- `position:absolute;` 设置元素为绝对定位
- `position:fixed;` 设置元素为固定定位


---

## 元素框

元素从外到内依次为：**外边距(margin) > 边框(border) > 内边距(padding) > 内容(content)**

**外边距**

1. 默认透明。
2. 上下两元素外边距取最大值：外边距重叠。
3. 可以取负值：相邻两元素重叠。

- `margin-top:30px;`

- `border:`

---

## 元素放置

### 元素类型

1. **块级元素**
   
  独占一行，可任意规定宽高和内外边距大小。宽度默认是容器的100%，以容纳内联元素和其他块元素。

  常用的块状元素有：`<div>、<p>、<h1>...<h6>、<ol>、<ul>、<dl>、<table>、<address>、<blockquote> 、<form>`

2. **内联元素** 

  和其他内联元素分享一行，宽高随内容自动变化（无法设置），不可设置上下边距大小，但可以设置左右边距。行内元素内不能容纳块元素。

  常用的行内元素有：`<a>、<span>、、<i>、<em>、<strong>、<label>、<q>、<var>、<cite>、<code>`

3. **行内块状元素**

同时具备行内元素、块状元素的特点，和其他元素在同一行，高、宽和边距可以设置。

常用元素 `<img>、<input>`


- `display:block` 设置为块级元素
- `display:inline` 设置为内联元素
- `display:inline-block` 设置为行内块级元素
- `display:none` 不摆放该元素


### 元素居中

在父级父容器中让行内元素居中对齐：

1. **水平居中**

对于块状元素，只需要规定 `margin: 0 auto` ;

对于内联元素（如文本和链接），只需要在块级父容器中添加 `text-align: center` 。

2. **垂直居中**

对于已知高度的块状元素，通过

```css
.parent { position: relative; } 

.child { 
  position: absolute; 
  top: 50%; 
  height: 100px; 
  margin-top: -50px; 
}
```

对于未知高度的块状元素，通过

```css
.parent { position: relative; } 

.child { 
  position: absolute; 
  top: 50%; 
  transform: translateY(-50%); 
}
```


对于内联元素，只需为它们添加等值的 padding-top 和 padding-bottom 就可以实现垂直居中。



特殊技巧：放在页面中央。

```css
.element {
  left:50%;
  top:50%;
  transform: translate(-50%,-50%);
}
```


### 元素隐藏

*隐藏元素共有三种方式，效果不同：*

- `display:none` 元素不摆放，等同于没有
- `visibility:hidden` 元素隐藏，不可用但仍占用布局空间
- `opacity:0` 元素透明，可用但不可见（不透明度0-1）

---

## 文本样式


### 文本间距

`letter-spacing:0.5em;` 字符间距
`word-spacing:0.5em;` 单词间距（只对英文起作用）


### 文本溢出

`overflow-x:hidden;`  水平方向：文本超出内容框后隐藏
`overflow-y:auto;`  垂直方向：视情况提供滚动条

参数|含义
-|-
visible | 文本超出后会显示在内容框之外。
hidden|  文本超出部分被裁剪。
scroll| 提供滚动条。	
auto| 文本超出后提供滚动条。
no-display| 如果内容不适合内容框，则删除整个框。
no-content| 如果内容不适合内容框，则隐藏整个内容。


---

## 表格样式

#### 边框

**table有外边距，但有无内边距视情况而定：**

`border-collapse:separate;`

【默认情况】表格内单元格(td)分散，表格内边距(padding)有效。

`border-collapse:collapse;`

【常用情况】表格内单元格(td)紧挨，且单元格和表格边框紧密贴合：表格内边距(padding)无效。

`border-space:0;`

单元格分散情况下调整单元格间距。设为0时可达到单元格紧挨效果。

【不绘制单元格框线，且需要设置表格内边距时可使用。】

**tr和td/th作为内部元素无外边框。**

#### 局部元素

tr：对 table 的指定行采用不同样式

`table tr:nth-child(odd){ background: #eeeff2; }`  //奇数行元素
`table tr:nth-child(even){ background: #dfe1e7; }`  //偶数行元素

td：对 table 的指定列采取不同样式

`tr td:nth-child(-n+3){ text-align:center; }`    //第3列以前的全部元素
`tr td:nth-child(n+4){ text-align:center; }`    //第4列以后的全部元素


