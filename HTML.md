# HTML

##  标签及使用

### 块级元素表

| 元素名称 |             作用             | 元素名称 |        作用        |
| :------: | :--------------------------: | :------: | :----------------: |
| address  |           定义地址           |   form   |   创建 html 表单   |
| caption  |           表格标题           |  h1-h6   |    一至六级标题    |
|    dd    |        列表中定义条目        |    hr    |   显示一条分割线   |
|   div    |     定义文档中的分区或节     |   pre    |   预格式化的文本   |
|    dl    |           定义列表           |    li    |    定义列表项目    |
|    dt    |       定义列表中的项目       |    ol    |      有序列表      |
| fieldset |         表单元素分组         |    ul    |      无序列表      |
|  legend  | 充当 `fieldset` 中内容的标题 |    p     |      定义段落      |
|  table   |        定义 HTML 表格        |    tr    |     表格中的行     |
|  tbody   |       表格主体（正文）       |    td    | 表格中的标准单元格 |
|  tfoot   |          表格的页脚          |  thead   |     表格的表头     |

### 行内元素表

| 元素名称 |        作用        | 元素名称 |             作用             |
| :------: | :----------------: | :------: | :--------------------------: |
|    a     |        链接        |   del    |            删除线            |
|   abbr   |        缩写        |    em    |             强调             |
| acronym  |       首字母       |   font   |       字体设定(不推荐)       |
|    b     |    粗体(不推荐)    |    i     |             斜体             |
|   bdo    | 覆盖默认的文本方向 |   img    |             图片             |
|   big    |       大字体       |  input   |            输入框            |
|    br    |        换行        |   kbd    |         定义键盘文本         |
|  button  |        按钮        |  label   |           表格标签           |
|   cite   |        引用        |    q     |            短引用            |
|   code   |     计算机代码     |   samp   |      定义范例计算机代码      |
|   dfn    |      定义字段      |  select  |           项目选择           |
|  small   |     小字体文本     |   span   | 常用内联容器，定义文本内区块 |
|  strike  |       中划线       |  strong  |           粗体强调           |
|   sub    |        下标        |   sup    |             上标             |
| textarea |   多行文本输入框   |    tt    |           电传文本           |
|    u     |       下划线       |   var    |           定义变量           |



### main 主内容

`<main></main>`标签标识html主要内容，通常放置于body标签下

### meta

```html
https://blog.csdn.net/tashanhongye/article/details/50197807<!-- meta详解-->
```

### section 区块

`<section></section>`标识内容不同部分

### a 链接

`<a></a>`标签用于创建一个链接，示例

```html
<a target="_blank" href="https://freecatphotoapp.com" text-decoration:none;>cat photos</a>
```

`target="_blank"`代表在新窗口打开链接。`href=""`为链接地址。`text-decoration:none;`去除下划线

### ul ol 列表

`<ul></ul>`无序列表

`<ol></ol>`有序列表

以上通常结合`<li></li>`列表元素使用，如下

```html
<ol>
  <li>Coffee</li>
  <li>Tea</li>
  <li>Milk</li>
</ol>

<ul>
  <li>Coffee</li>
  <li>Tea</li>
  <li>Milk</li>
</ul>
```

```css
list-style:none;/*将列表前原点去除*/
```



### figure 图像封装

`<figure></figure>`用于封装图像、图表、视频等元素，移除它也没影响，主要是更规范

且有助于搜索引擎找到这些图像。

通常和`<figcaption>标题用于描述figure元素中包含的图像</figcaption>`联用

###  em strong 强调及加粗

```html
<em>强调文本</em><br>
<strong>加粗文本</strong><br>
```

### br换行

`<br>`插入一个换行符，无结束标签

### form 表单

`<form></form>`用于创建供用户输入的 HTML 表单,action属性用于标记表单内容提交至何处`method`属性规定用于发送表单数据的 HTTP 方法

`target`规定在何处打开 action URL

```html
<form action="demo_form.php" method="get" target="_blank">
  First name: <input type="text" name="fname"><br>
  Last name: <input type="text" name="lname"><br>
  <input type="submit" value="提交">
</form>
```

通常与以下标签联合使用

```html
<input> 自闭和，无结束标签 type属性标记input类型,可以是文本，按钮等

<textarea> textarea 元素和 text 类型的 input 类似，区别是 textarea 有一些额外的好处，比如可以方便地添加更多的文本以及设置默认显示的行数和列宽。
    
<button> 按钮
<select> 下拉列表 
<option> 下拉列表中的一个选项（一个条目）
    
<optgroup> 选项分类，更贴切地说二级标签
    <optgroup label="German Cars">
    <option value="mercedes">Mercedes</option>
    <option value="audi">Audi</option>
</optgroup>
```

### input 输入

`<input>`自闭和，无结束标签。type属性标记input类型,可以是文本，按钮,多选框checkbox等，`placeholder`占位符文本用于提示人们在输入框中输入什么样的信息,

`required`为了防止用户在缺少所需信息时提交你的表单无需为 `required` 属性设置值。 只需将单词 `required` 添加到 `input` 元素，确保它和其他属性之间有空格。

`checked` 默认选中

```html
<input type="text" name="catphotourl" placeholder="cat photo URL" required>
```

name属性可以相同id必须不同，以此可以做到两个相同按钮同一时间只可以点亮一个。

```html
<label><input id="indoor" type="radio" name="indoor-outdoor"> Indoor</label>
<label><input id="outdoor" type="radio" name="indoor-outdoor"> Outdoor</label>
```

### button 按钮

`<button></button>`定义一个按钮,type属性标识按钮的类型

```html
button 按钮
reset	重置表单
submit  提交表单
```

### label

`<label></label>`用于帮助将 `input` 元素的文本与 `input` 元素本身关联起来,使用for属性

```html
<label for="first-name">Enter Your First Name: <input id="first-name"/></label>
```

也就是说点击文本和点击input按钮是一样的

### fieldset

`<fieldset></fieldset>`将表单内的相关元素分组，在相关表单元素周围绘制边框

`<legend></legend>`充当 `fieldset` 元素中内容的标题

### hr 分割线

显示一条分割线

### meta

为了使页面样式在移动端看起来与在桌面或笔记本电脑上相似，你需要添加一个带有特殊 `content` 属性的 `meta` 元素。

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

### div 容器

```html
<div id="" class="" aria-hidden="true">
</div>
```

在作为页面弹窗的元素(modal)上，通常会有一个属性`aria-hidden="true"`，那么它有什么作用呢？

**让这个元素对浏览器隐藏**。这里的“隐藏”更多指的是**语义化的隐藏**，常用于配合厂商的无障碍阅读，而实现弹窗效果需配合display: none等样式使用。



### div、secton、article

#### 区别

* div、section、article：语义从无到有，逐渐增强。
* div：无任何语义，仅用作样式化和脚本化的标签。
* section：主要用于一段主题性内，一般包括一个或多个标题。
* article： 主要描述一段具体的、可以脱离上下文的内容。

#### 注意事项

* 设置样式时，使用 div 。     
* 可以用 article、aside、nav 等元素时，不使用 section 。
* 没有标题的内容区块，不使用 section 。

### header 页眉

```html
<header> 标签定义文档或者文档的一部分区域的页眉。

<header> 元素应该作为介绍内容或者导航链接栏的容器。

在一个文档中，您可以定义多个 <header> 元素。

注释：<header> 标签不能被放在 <footer>、<address> 或者另一个 <header> 元素内部。
```

### span

`<span>`在行内定义一个区域，也就是一行内可以被`<<span>>`划分成好几个区域，从而实现某种特定效果。`<span>`本身没有任何属性。

####  span 和 div 区别

1. div标签是块级元素，拥有块级元素的特点。每对div标签（<div></div>）里的内容都可以占据一行，不会其他标签在一行显示；div标签总是从新行开始显示；
2. 且div标签可以通过css样式来设置自身的宽度（也可省略，当没有使用css自定义宽度时，div标签的宽度为其的容器的100%）、高度，且还可以设置标签之间的距离（外边距和内边距）；
3. <span>标签是行内元素，拥有行内元素的特点。<span>标签元素会和其他标签元素会在一行显示（块级元素除外），不会另起一行显示（如上例）。
4. <span>标签的宽度、高度都无法通过css样式设置，它的宽高受其本身内容（文字、图片）控制，随着内容的宽高改变而改变；<span>标签无法控制外边距和内边距，虽然可以设置左右的外边距和内边距，但上下的外边距和内边距无法设置。

### i 斜体

标签内文字变为斜体

**注意，`<i>` 标签虽然传统上用来强调文本，但此后常用作图标的标签。 `<em>` 标签作为强调标签现在已被广泛接受。**



### nav 导航区链接

`<nav>` 标签定义导航链接的部分。

并不是所有的 HTML 文档都要使用到 `<nav>` 元素。`<nav>` 元素只是作为标注一个导航链接的区域。

在不同设备上（手机或者PC）可以制定导航链接是否显示，以适应不同屏幕的需求。

### svg 矢量图像

*SVG*（可缩放矢量图形）的一个有用之处是它包含一个 `path` 属性，该属性允许在不影响图像分辨率的情况下缩放图像。

CSS has a `max` function which returns the largest of a set of comma-separated values. For example:

```css
img {
  width: max(250px, 25vw);
}
```

随着窗口增大，面积变为25%。



### figure 图像区域

```html
<figure>
  <img decoding="async" fetchpriority="high" src="img_pulpit.jpg" alt="The Pulpit Rock" width="304" height="228">
</figure>
```

标签规定独立的流内容（图像、图表、照片、代码等等）。
 元素的内容应该与主内容相关，同时元素的位置相对于主内容是独立的。如果被删除，则不应对文档流产生影响。

#### figcaption 图像标题



### table 表格

```html
<table border="1">
  <tr>
    <th>Month</th>
    <th>Savings</th>
  </tr>
  <tr>
    <td>January</td>
    <td>$100</td>
  </tr>
  <tr>
    <td>February</td>
    <td>$80</td>
  </tr>
</table>
```

<table> 标签定义 HTML 表格

一个 HTML 表格包括 `<table>` 元素，一个或多个` <tr>`、`<th>` 以及 `<td> `元素。

`<tr> `元素定义表格行，`<th> `元素定义表头，`<td> `元素定义表格单元。

`<tbody>` 元素应该与 and 元素结合起来使用，用来规定表格的各个部分（主体、表头、页脚）。

#### caption 表格标题

`<caption>` 标签定义表格的标题。

`<caption>` 标签必须直接放置到 `<table> `标签之后。

您只能对每个表格定义一个标题。

**提示：**通常这个标题会被居中于表格之上。然而，CSS 属性 "text-align" 和 "caption-side" 能用来设置标题的对齐方式和显示位置。



### code 代码

`<code>` 标签是一个短语标签，用来定义计算机代码文本。

### nav 导航

<nav> 标签定义导航链接的部分。

并不是所有的 HTML 文档都要使用到 `<nav>` 元素。`<nav>` 元素只是作为标注一个导航链接的区域。

### blockquote 块引用

blockquote标签定义摘自另一个源的块引用。

如果标记是不需要段落分隔的短引用，请使用`q`

### aside 侧边

`aside` 标签定义 `article `标签外的内容。

`aside` 的内容应该与附近的内容相关。

**提示：**`<aside> `的内容可用作文章的侧栏。

## HTML标签name id class 属性辨析

HTML 标签中的 `name`、`id` 和 `class` 属性分别具有不同的作用，下面是它们的详细说明和示例：

1. **`name` 属性**：

   - `name` 属性通常用于表单元素，以定义元素的名称，这有助于在提交表单时标识表单字段。

   - `name` 属性通常用于输入字段、单选按钮、复选框、下拉框等，以便将其值发送到服务器。

   - 示例：

     ```html
     <form action="submit.php" method="post">
       <label for="username">Username:</label>
       <input type="text" id="username" name="username">
       <input type="submit" value="Submit">
     </form>
     ```

   在上面的示例中，`name` 属性用于标识用户名输入字段，以便在提交表单时将用户名值发送到服务器。

   **如果一个表单字段（例如输入字段）没有 `name` 属性，那么在提交表单时，该字段的值将不会被发送到服务器。只有具有 `name` 属性的表单字段的值才会随表单一起提交。**

2. **`id` 属性**：

   - `id` 属性用于为 HTML 元素指定唯一的标识符，通常用于 JavaScript 和 CSS 中，以便能够通过标识符来访问或操作特定的元素。

   - `id` 属性的值必须在整个 HTML 文档中是唯一的，不同的元素不应该拥有相同的 `id`。

   - 示例：

     ```html
     <div id="myDiv">This is a div with an ID.</div>
     <script>
       var divElement = document.getElementById('myDiv');
       // 使用 JavaScript 访问具有特定 ID 的元素
     </script>
     ```

   在上面的示例中，`id` 属性用于标识特定的 `<div>` 元素，以便在 JavaScript 中访问该元素。

3. **`class` 属性**：

   - `class` 属性用于为 HTML 元素定义一个或多个类名，通常用于应用样式或选择一组相关的元素。

   - `class` 属性的值可以在多个元素中共享，允许多个元素应用相同的样式或属性。

   - 示例：

     ```html
     <ul>
       <li class="menu-item">Item 1</li>
       <li class="menu-item">Item 2</li>
       <li class="menu-item">Item 3</li>
     </ul>
     <style>
       .menu-item {
         color: blue;
       }
     </style>
     ```

   在上面的示例中，`class` 属性用于为列表项 `<li>` 元素应用相同的类名，以便通过样式表将它们的文本颜色设置为蓝色。

总之，`name` 属性用于标识表单字段的名称，`id` 属性用于唯一标识元素，而 `class` 属性用于将多个元素分组并应用相同的样式或属性。这些属性在 HTML、JavaScript 和 CSS 中有不同的用途，根据需要进行选择和使用。
