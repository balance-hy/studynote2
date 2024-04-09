# jQuery

> https://jquery.cuishifeng.cn/

首先，在页面顶部添加 `script` 标签， 记得在后面为它添加结束标签。

浏览器会运行 `script` 标签所有的 JavaScript 脚本包括 jQuery。

在 `script` 标签中添加代码 `$(document).ready(function() {`。 然后在后面（仍在该 `script` 标签内）用 `});` 闭合它。

稍后将详细介绍 `functions`， 重要的是要知道，在浏览器加载页面后，你放入此 `function` 的代码将立即运行。

有一点很重要，如果没有 `document ready function`，代码将在 HTML 页面呈现之前运行，这可能会导致错误。

```html
<script type="text/javascript">
    	$(document).ready(function() {});
</script>
```

现在我们有一个 `document ready` 函数。

首先，完成第一个 jQuery 语句。 **所有的 jQuery 函数都以 `$` 开头**，这个符号通常被称为美元符号（dollar sign operator）或 bling。

jQuery 通常选取并操作带有选择器（selector）的 HTML 标签。

比如，想要给 `button` 元素添加跳跃效果。 只需要在 document ready 函数内添加如下代码：

```js
$("button").addClass("animated bounce");
```

请注意，我们已经在后台引入了 jQuery 库和 Animate.css 库，所以你可以在编辑器里直接使用它们。 你将使用 jQuery 将 Animate.css `bounce` class 应用于 `button` 元素。

如何使所有的 `button` 标签都有弹跳的动画效果？ 用 `$("button")` 选取所有的 button 标签，并用 `.addClass("animated bounce");` 给其添加一些 CSS 属性。

## $ 符号

在 jQuery 中，`$` 符号是一个非常重要的标识符，它通常用来代替全名的 `jQuery` 对象。 `$` 符号是一个合法的 JavaScript 变量名，可以用于创建 jQuery 对象、选择元素以及执行各种 DOM 操作。这是为了简化代码和提高可读性而引入的。

下面是一些常见的 `$` 符号在 jQuery 中的用法：

1. **创建 jQuery 对象**：使用 `$` 符号来包装一个或多个 DOM 元素，创建 jQuery 对象，以便后续进行 jQuery 操作。

   ```js
   var $element = $("#myElement"); // 选择元素并创建 jQuery 对象
   ```

2. **选择元素**：使用 `$` 符号作为选择器，可以选择一个或多个 DOM 元素。

   ```js
   var $paragraphs = $("p"); // 选择所有 <p> 元素
   ```

3. **执行 DOM 操作**：使用 `$` 符号来执行各种 DOM 操作，如添加、删除、修改元素的属性或内容。

   ```js
   $element.addClass("highlighted"); // 添加 CSS 类
   $element.text("New text"); // 设置元素文本内容
   ```

4. **事件处理**：使用 `$` 符号来绑定事件处理程序。

   ```js
   $element.click(function() {
     // 处理点击事件
   });
   ```

5. **Ajax 请求**：使用 `$` 符号的 AJAX 方法来执行异步请求。

   ```js
   $.ajax({
     url: "example.com/data",
     success: function(data) {
       // 处理响应数据
     }
   });
   ```

总之，`$` 符号是 jQuery 的核心标识符，用于引用 jQuery 对象、执行 DOM 操作和处理事件等。它有助于简化和提高 JavaScript 代码的可读性。请注意，虽然 `$` 符号通常用于 jQuery，但它本质上只是一个 JavaScript 变量，因此你可以在代码中自定义 `$` 变量，但这可能导致冲突，所以谨慎使用。

### $使用

三种选取标签的方法：用元素选择器：`$("button")`、用类选择器：`$(".btn")` 以及用 id 选择器：`$("#target1")` 。

#### 选择第n个子元素

已经看到了 id 属性对于 jQuery 选择器选取标签的便利， 但这并不适用于所有情景。

幸运的是，jQuery 有一些其他的方法可以选取正确的标签。

jQuery 可以用 CSS 选择器（CSS Selectors）选取标签。 `target:nth-child(n)` CSS 选择器可以选取指定 class 或者元素类型的的第 n 个标签。

下面的代码展示了给每个区域（well）的第 3 个标签设置弹跳（bounce）动画效果：

```js
$(".target:nth-child(3)").addClass("animated bounce");
```

#### 选择偶数或者奇数的子元素

也可以用基于位置的奇 `:odd` 和偶 `:even` 选择器选取标签。

注意**，jQuery 是零索引（zero-indexed）的，这意味着第 1 个标签的位置编号是 0。** 这有点混乱和反常——`:odd` 表示选择第 2 个标签（位置编号 1）、第 4 个标签（位置编号 3）……等等，以此类推。

下面的代码展示了选取所有 `target` class 的奇数元素并给它们设置 class：

```js
$(".target:odd").addClass("animated shake");
```

## Dom元素

DOM 元素是文档对象模型（Document Object Model）中的基本构建块，代表网页中的各种元素或节点。DOM 元素是网页上可操作的基本单位，它们可以是 HTML 元素（如段落、标题、按钮等）或文档中的其他部分（如文本、属性等）。每个 DOM 元素都是文档树的一部分，可以通过 JavaScript 来访问、操作和修改。

以下是一些关于 DOM 元素的要点：

1. **HTML 元素**：HTML 元素是 DOM 中的一种元素，它们代表网页中的标记元素，如 `<p>`、`<div>`、`<a>`、`<button>` 等。这些元素具有各种属性和方法，可以通过 JavaScript 来操纵和操作。
2. **文本元素**：文本元素是 DOM 元素的一种，它包含纯文本内容，例如段落中的文字。你可以使用 JavaScript 来获取、修改和操作文本元素的内容。
3. **属性**：DOM 元素可以有各种属性，这些属性包括元素的 ID、类名、样式、事件处理程序等。通过 JavaScript，你可以访问和修改这些属性。
4. **节点**：DOM 元素是 DOM 树中的节点，DOM 树是网页的逻辑结构表示。每个 DOM 元素都有父节点、子节点和兄弟节点。你可以使用 JavaScript 遍历 DOM 树，访问不同的元素。
5. **操作元素**：通过 JavaScript，你可以执行各种操作，如创建、删除、插入和移动元素。这使你能够实时更新网页内容。
6. **事件处理**：DOM 元素可以关联事件处理程序，以便在特定事件发生时执行 JavaScript 代码。例如，你可以将点击事件处理程序附加到按钮元素，以便在用户点击按钮时执行特定操作。

总之，DOM 元素是构成网页的基本组成部分，通过 JavaScript 可以实现对这些元素的访问、操作和控制，以实现动态和交互性的网页功能

## 方法

### addClass 添加类

jQuery 的 `.addClass()` 方法用来给标签添加类。

### removeClass 移除类

和用 jQuery 的 `addClass()` 方法给标签添加类一样，也可以利用 jQuery 的 `removeClass()` 方法移除它们。

下面是为指定按钮执行上面的操作的代码：

```js
$("#target2").removeClass("btn-default");
```

### remove 移除html元素

jQuery 有一个名为 `.remove()` 的函数，它将完全删除一个 HTML 元素。

```js
$("#target4").remove()
```

### css()改变标签的css

下面的代码效果是把颜色变蓝：

```js
$("#target1").css("color", "blue");
```

这与通常的 CSS 声明略有不同，因为这个 **CSS 属性和它的值在英文引号里，并且它们用逗号而不是冒号间隔开**。

### prop 调整标签的属性

下面是禁用所有的按钮的代码：

```js
$("button").prop("disabled", true);
```

### html 在标签里添加Html内容

jQuery 有一个 `.html()` 函数，能用其在标签里添加 HTML 标签和文本， 函数提供的内容将完全替换之前标签的内容。

下面是重写并强调标题文本的代码：

```js
$("h3").html("<em>jQuery Playground</em>");
```

### text 获取或设置元素的文本

在 jQuery 中，`.text()` 方法用于获取或设置元素的文本内容。它可以用于读取元素的文本内容或将新的文本内容设置给元素。以下是 `.text()` 方法的用法示例：

**获取元素的文本内容**：

```js
// 获取元素的文本内容
var text = $("p").text();
console.log(text); // 输出段落元素的文本内容
```

上面的示例通过选择所有 `<p>` 元素，并使用 `.text()` 方法获取它们的文本内容。获取的文本内容将作为字符串返回。

**设置元素的文本内容**：

```js
// 设置元素的文本内容
$("p").text("新的文本内容");
```

上面的示例将所有 `<p>` 元素的文本内容设置为 "新的文本内容"。

使用 `.text()` 方法是一种方便的方式来访问和修改元素的文本内容。这对于更新页面上的文本内容或读取用户输入的文本非常有用。**请注意，`.text()` 方法只会获取或设置元素的文本内容，不会处理 HTML 标签或解析 HTML，它会将任何包含在元素中的 HTML 标签作为纯文本对待。如果需要操作包含 HTML 标签的内容，可以考虑使用 `.html()` 方法。**

### appendTo 将一个标签添加到另一个标签里面

```js
$("#target2").appendTo("#right-well");
```

### clone 复制标签

例如，如果想把 `target2` 从 `left-well` 复制到 `right-well`，可以设置如下：

```js
$("#target2").clone().appendTo("#right-well");
```

是否注意到这两个 jQuery 函数连在一起了？ 这被称为链式调用（function chaining），是一种用 jQuery 实现效果的简便方法。

### parent 访问被选取标签的父标签

每个 HTML 标签都默认 `inherits`（继承）其 `parent`（父标签）的 CSS 属性。

例如，你的 `jQuery Playground` 中的 `h3` 标签的父标签是 `<div class="container-fluid">`，而这个标签的父标签是 `body`。

jQuery 有一个 `parent()` 方法，可以访问被选取标签的父标签。

下面的代码展示了使用 `parent()` 方法把 `left-well` 标签的父标签背景色设置成蓝色（blue）：

```js
$("#left-well").parent().css("background-color", "blue")
```

### children 访问被选取标签的子标签

把 HTML 标签放到另一个级别的标签里，这些 HTML 标签被称为该标签的子标签（children element）。 例如，本次挑战中文本为 `#target1`、`#target2` 和 `#target3` 的按钮都是 `<div class="well" id="left-well">` 标签的子标签。

jQuery 有一个 `children()` 方法，可以访问被选取标签的子标签。

下面的代码展示了用 `children()` 方法把 `left-well` 标签的子标签的颜色设置成 `blue`（蓝色）：

```js
$("#left-well").children().css("color", "blue")
```

 ### show()/hide()

显示/隐藏