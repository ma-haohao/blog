# JavaScript基础教程

## 1. JavaScript的简介

### 用法

HTML 中的脚本必须位于 <script>与</script> 标签之间。

脚本可被放置在 HTML 页面的 <body> 和 <head> 部分中。

### 在 <head\> 或者 <body\> 的JavaScript 

您可以在 HTML 文档中放入不限数量的脚本。

脚本可位于 HTML 的 <body> 或 <head> 部分中，或者同时存在于两个部分中。

通常的做法是把函数放入 <head> 部分中，或者放在页面底部。这样就可以把它们安置到同一处位置，不会干扰页面的内容。

### JavaScript 函数和事件

对于某些 JavaScript 语句，会在页面加载时执行。

通常，我们需要在某个事件发生时执行代码，比如当用户点击按钮时。

如果我们把 JavaScript 代码放入函数中，就可以在事件发生时调用该函数。

### 外部的 JavaScript

也可以把脚本保存到外部文件中。外部文件通常包含被多个网页使用的代码。

外部 JavaScript 文件的文件扩展名是 .js。

如需使用外部文件，请在 <script> 标签的 "src" 属性中设置该 .js 文件：

```html
<!DOCTYPE html>
<html>
<head> 
<meta charset="utf-8"> 
<title>菜鸟教程(runoob.com)</title> 
</head>
<body>
	
<h1>我的 Web 页面</h1>
<p id="demo">一个段落。</p>
<button type="button" onclick="myFunction()">点击这里</button>
<p><b>注释：</b>myFunction 保存在名为 "myScript.js" 的外部文件中。</p>
<script src="myScript.js"></script>
	
</body>
</html>
```

外部脚本不能包含 <script> 标签。

### 输出方式

JavaScript 没有任何打印或者输出的函数，但是可以通过以下不同的方式来输出数据：

- 使用 **window.alert()** 弹出警告框。
- 使用 **document.write()** 方法将内容写到 HTML 文档中。
- 使用 **innerHTML** 写入到 HTML 元素。
- 使用 **console.log()** 写入到浏览器的控制台。

## 2. JavaScript基础语法

略

## 3. DOM介绍

HTML DOM 定义了访问和操作 HTML 文档的标准方法。

DOM 将 HTML 文档表达为树结构。

HTML DOM 定义了所有 HTML 元素的*对象*和*属性*，以及访问它们的*方法*。

**换言之，HTML DOM 是关于如何获取、修改、添加或删除 HTML 元素的标准。**

### HTML DOM 树

![HTML DOM Node Tree](https://www.w3school.com.cn/i/ct_htmltree.gif)

通过 HTML DOM，树中的所有节点均可通过 JavaScript 进行访问。所有 HTML 元素（节点）均可被修改，也可以创建或删除节点。

DOM 处理中的常见错误是希望元素节点包含文本。

在本例中：*<title>DOM 教程</title>*，元素节点 <title>，包含值为 "文档标题" 的*文本节点*。

可通过节点的 *innerHTML* 属性来访问文本节点的值。

### HTML DOM 方法和属性

**方法是我们可以在节点（HTML 元素）上执行的动作。**

**属性是节点（HTML 元素）的值，您能够获取或设置。**

#### 编程接口

可通过 JavaScript （以及其他编程语言）对 HTML DOM 进行访问。

所有 HTML 元素被定义为对象，而编程接口则是对象方法和对象属性。

方法是您能够执行的动作（比如添加或修改元素）。

属性是您能够获取或设置的值（比如节点的名称或内容）。

#### HTML DOM 对象 - 方法和属性

**一些常用的 HTML DOM 方法：**

- getElementById(id) - 获取带有指定 id 的节点（元素）
- appendChild(node) - 插入新的子节点（元素）
- removeChild(node) - 删除子节点（元素）

**一些常用的 HTML DOM 属性：**

- innerHTML - 节点（元素）的文本值
- parentNode - 节点（元素）的父节点
- childNodes - 节点（元素）的子节点
- attributes - 节点（元素）的属性节点

### HTML DOM - 访问

访问 HTML 元素等同于访问节点

可以通过不同的方式来访问 HTML 元素：

- 通过使用 getElementById() 方法
- 通过使用 getElementsByTagName() 方法
- 通过使用 getElementsByClassName() 方法

**通过id来访问DOM:**

id在一个HTML中一般可以作为唯一标识符，因此getElementById() 方法可以返回带有指定 ID 的元素

**通过TagName来访问DOM:**

getElementsByTagName() 返回带有指定标签名的**所有元素**（可以通过x[i]来访问不同的元素）。

```javascript
node.getElementsByTagName("tagname");
//其中node为限制范围的节点，可以为document(这时相当于全文搜索)，也可以为特定节点下的元素，例如：
document.getElementById("main").getElementsByTagName("p");
//返回包含文档中所有 <p> 元素的列表，并且这些 <p> 元素应该是 id="main" 的元素的后代（子、孙等等）。
```

**通过ClassName来访问:**

getElementsByClassName() 返回带有指定类名下的**所有元素**（可以通过x[i]来访问不同的元素）。

### HTML DOM - 修改

**修改 HTML = 改变元素、属性、样式和事件。**

修改 HTML DOM 意味着许多不同的方面：

- 改变 HTML 内容
- 改变 CSS 样式
- 改变 HTML 属性
- 创建新的 HTML 元素
- 删除已有的 HTML 元素
- 改变事件（处理程序）

**改变HTML内容**

```html
<!DOCTYPE html>
<html>
<body>

<p id="p1">Hello World!</p>

<script>
document.getElementById("p1").innerHTML="New text!";
</script>

<p>上面的段落被一段脚本改变了。</p>

</body>
</html>
```

**改变CSS的样式**

```html
<!DOCTYPE html>
<html>
<body>

<p id="p1">Hello world!</p>
<p id="p2">Hello world!</p>

<script>
document.getElementById("p2").style.color="blue";
document.getElementById("p2").style.fontFamily="Arial";
document.getElementById("p2").style.fontSize="larger";
</script>

</body>
</html>
```

**创建新的HTML元素**

```html
<!DOCTYPE html>
<html>
<body>

<div id="div1">
<p id="p1">This is a paragraph.</p>
<p id="p2">This is another paragraph.</p>
</div>

<script>
<!--首先创建响应tagName的元素，同时创建的元素可以视为一个新的节点，之后把该元素挂到相应的父节点下面去-->
  
var para=document.createElement("p");
var node=document.createTextNode("This is new.");
para.appendChild(node);

var element=document.getElementById("div1");
element.appendChild(para);
</script>

</body>
</html>
```

**创建新的HTML元素-insertBefore()**

上一个例子中的 appendChild() 方法，将新元素作为父元素的最后一个子元素进行添加。

如果不希望如此，您可以使用 insertBefore() 方法：

```html
<!DOCTYPE html>
<html>
<body>

<div id="div1">
<p id="p1">This is a paragraph.</p>
<p id="p2">This is another paragraph.</p>
</div>

<script>
var para=document.createElement("p");
var node=document.createTextNode("This is new.");
para.appendChild(node);

var element=document.getElementById("div1");
var child=document.getElementById("p1");
element.insertBefore(para,child);
</script>

</body>
</html>
```

**删除已有的HTML元素**

如需删除 HTML 元素，您必须清楚该元素的父元素：

```html
<!DOCTYPE html>
<html>
<body>

<div id="div1">
<p id="p1">This is a paragraph.</p>
<p id="p2">This is another paragraph.</p>
</div>

<script>
var parent=document.getElementById("div1");
var child=document.getElementById("p1");
parent.removeChild(child);
</script>

</body>
</html>
```

**替换HTML元素**

如需替换 HTML DOM 中的元素，请使用 replaceChild() 方法：

```html
<!DOCTYPE html>
<html>
<body>

<div id="div1">
<p id="p1">This is a paragraph.</p>
<p id="p2">This is another paragraph.</p>
</div>

<script>
var parent=document.getElementById("div1");
var child=document.getElementById("p1");
var para=document.createElement("p");
var node=document.createTextNode("This is new.");
para.appendChild(node);
parent.replaceChild(para,child);
</script>

</body>
</html>
```

### HTML DOM - 事件

**HTML DOM 允许 JavaScript 对 HTML 事件作出反应。**

**对事件作出反应**

```html
<!--当用户点击时，会改变 <h1> 元素的内容：-->
<!DOCTYPE html>
<html>
<body>

<h1 onclick="this.innerHTML='hello!'">请点击这段文本！</h1>

</body>
</html>
```

**通过从事件处理程序中来调用函数:**

```html
<!DOCTYPE html>
<html>
<head>
<script>
function changetext(id){
  id.innerHTML="hello!"
}
</script>
</head>
<body>
<h1 onclick="changetext(this)">请点击这段文本!</h1>
</body>
</html>
```

**使用 HTML DOM 来分配事件**

HTML DOM 允许您使用 JavaScript 向 HTML 元素分配事件：

例: 为 button 元素分配 onclick 事件：

```html
<!DOCTYPE html>
<html>
<head>
</head>
<body>

<p>点击按钮来执行 <b>displayDate()</b> 函数。</p>

<button id="myBtn">试一试</button>

<script>
document.getElementById("myBtn").onclick=function(){displayDate()};
function displayDate()
{
document.getElementById("demo").innerHTML=Date();
}
</script>

<p id="demo"></p>

</body>
</html>
```

在上面的例子中，名为 displayDate 的函数被分配给了 id=myButn" 的 HTML 元素。

当按钮被点击时，将执行函数。

## 4.类的基本知识

