# JavaScript

由浏览器（或者Node.js）执行的动态函数，解析后对 DOM 树元素进行动态修改，以实现页面的动态功能。

---

## 相等判定

- == 值是否相等 `'10' == 10`
- === 值及类型是否完全相等 `'10' !== 10`

## 变量定义

**var 变量**

如果定义在函数内，不能跨函数访问。

**let 块内变量**

不仅不能跨函数访问，如果定义在块内，也不能跨块访问。

**const 常量**

必须初始化(即必须赋值)，不能跨块访问，而且不能修改。

<font size=2 color=brown>非基础类型的 const 对象实际是保存指向对象的指针，修改对象的内容是允许的。</font>

## 数据交互 Ajax

使用现有的 js 语法，通过在后台与服务器进行少量数据交换，AJAX 可以使网页实现异步更新。即在不重新加载整个网页的情况下，对网页的某部分进行更新。

XMLHttpRequest 是 AJAX 的基础，通过其在后台和服务器间交换数据。

```js
function loadXMLDoc()
{
  //创建交互对象
  var xmlhttp=new XMLHttpRequest();
  //修改页面数据
  xmlhttp.onreadystatechange=function()
  {
    if (xmlhttp.readyState==4 && xmlhttp.status==200)
    {
      document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
    }
  }
  //发送请求（请求类型/URL/是否异步）
  xmlhttp.open("GET","/try/ajax/ajax_info.txt",true);
  xmlhttp.send();
}
```

用jQuery可以实现，代码如下：

```js
$(function()
{
  $.ajax({
      url: "show.html",    //目标页面
      dataType: "html",
      type: "GET",
      cache: false,
      success: function(html){
        $("html").html(html);
      }
    });
});
```