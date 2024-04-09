# Sass

Sass，英文全称为 “Syntactically Awesome StyleSheets”，是对 CSS 的扩展。它添加了一些基础 CSS 中不具有的新特性。Sass 简化了 CSS，这让维护项目中的样式表变得更容易。

`<style type='text/scss'>`

## 变量

Sass 不同于 CSS 的一个特点是它允许使用变量。 可以在 Sass 中声明变量，并为它赋值，就像在 JavaScript 中一样。(css也可以使用变量啊，只不过稍微繁琐一点)

在 JavaScript 中，变量是使用 `let` 和 `const` 关键字定义的。 在 Sass 中，变量以 `$` 开头的，后跟变量名。

这里有几个例子：

```scss
$main-fonts: Arial, sans-serif;
$headings-color: green;
```

并使用变量：

```scss
h1 {
  font-family: $main-fonts;
  color: $headings-color;
}
```

当需要把多个元素设置成相同颜色时，变量就会很有用。 一旦需要更改颜色，只需要改变这个变量的值就好。

## 嵌套CSS规则 好！

Sass 允许 CSS 规则的嵌套，这在组织样式表的时候会很有用。

在 CSS 里，每个元素的样式都需要写在独立的代码块中，如下所示：

```scss
article {
  height: 200px;
}

article p {
  color: white;
}

article ul {
  color: blue;
}
```

对于一个大型项目，CSS 规则会很复杂。 这时，引入嵌套功能（即在对应的父元素中写子元素的样式）可以有效地简化代码：

```scss
article {
  height: 200px;

  p {
    color: white;
  }

  ul {
    color: blue;
  }
}
```

## @mixin 复用CSS

在 Sass 中，mixin 是一组 CSS 声明，可以在整个样式表中重复使用。

CSS 的新功能需要一段时间适配后，所有浏览器后才能完全使用。 随着浏览器的不断升级，使用这些 CSS 规则时可能需要添加浏览器前缀。 考虑 `box-shadow`：

```scss
div {
  -webkit-box-shadow: 0px 0px 4px #fff;
  -moz-box-shadow: 0px 0px 4px #fff;
  -ms-box-shadow: 0px 0px 4px #fff;
  box-shadow: 0px 0px 4px #fff;
}
```

对于所有具有 `box-shadow` 属性的元素重写此规则，或者更改每个值以测试不同的效果，需要花费大量的精力。 Mixins 就像 CSS 的函数。 以下是一个例子：

```scss
@mixin box-shadow($x, $y, $blur, $c){ 
  -webkit-box-shadow: $x $y $blur $c;
  -moz-box-shadow: $x $y $blur $c;
  -ms-box-shadow: $x $y $blur $c;
  box-shadow: $x $y $blur $c;
}
```

定义以 `@mixin` 开头，后跟自定义名称。 参数（`$x`，`$y`，`$blur`，以及上例中的 `$c` ）是可选的。 **现在在需要 `box-shadow` 规则的地方，只需一行 mixin 调用而无需添加所有的浏览器前缀。 mixin 可以通过 `@include` 指令调用。**

```scss
div {
  @include box-shadow(0px, 0px, 4px, #fff);
}
```

## @if @else if @else 样式添加逻辑

Sass 中的 `@if` 指令对于测试特定情况非常有用——它的工作方式与 JavaScript 中的 `if` 语句类似。

```scss
@mixin make-bold($bool) {
  @if $bool == true {
    font-weight: bold;
  }
}
```

类似 JavaScript，`@else if` 和 `@else` 指令测试更多条件：

```scss
@mixin text-effect($val) {
  @if $val == danger {
    color: red;
  }
  @else if $val == alert {
    color: yellow;
  }
  @else if $val == success {
    color: green;
  }
  @else {
    color: black;
  }
}
```

## @for 循环

可以在 Sass 中使用 `@for` 循环添加样式，它的用法和 JavaScript 中的 `for` 循环类似。

`@for` 以两种方式使用：“开始 through 结束” 或 “开始 to 结束”。 主要区别在于“开始 **to** 结束”*不包括*结束数字，而“开始 **through** 结束”*包括*结束数字。

这是一个开始 **through** 结束的示例：

```scss
@for $i from 1 through 12 {
  .col-#{$i} { width: 100%/12 * $i; }
}
```

`#{$i}` 部分是将变量（`i`）与文本组合成字符串的语法。 当 Sass 文件转换为 CSS 时，它看起来像这样：

```scss
.col-1 {
  width: 8.33333%;
}

.col-2 {
  width: 16.66667%;
}

...

.col-12 {
  width: 100%;
}
```

这是创建网格布局的有效方法。 现在，有了 12 个可用作 CSS class 的列宽选项。

## @each 遍历列表或映射中的各项目

上一个挑战显示了 `@for` 指令如何通过起始值和结束值循环一定次数。 Sass 还提供 `@each` 指令，该指令循环遍历列表或映射中的每个项目。 在每次迭代时，变量将从列表或映射分配给当前值。

```scss
@each $color in blue, red, green {
  .#{$color}-text {color: $color;}
}
```

map 的语法略有不同。 这是一个示例：

```scss
$colors: (color1: blue, color2: red, color3: green);

@each $key, $color in $colors {
  .#{$color}-text {color: $color;}
}
```

请注意，需要用 `$key` 变量来引用 map 中的键。 否则，编译后的 CSS 将包含 `color1`，`color2`...... 以上两个代码示例都转换为以下 CSS：

```scss
.blue-text {
  color: blue;
}

.red-text {
  color: red;
}

.green-text {
  color: green;
}
```

## @while 循环

Sass 中的 `@while` 指令与 JavaScript 中的 `while` 类似。 只要满足条件，它就会一直创建 CSS 代码。

`@for` 挑战提供了一个创建简单网格系统的示例。 用 `@while` 也可以实现。

```scss
$x: 1;
@while $x < 13 {
  .col-#{$x} { width: 100%/12 * $x;}
  $x: $x + 1;
}
```

首先，定义变量 `$x` 并将其设置为 1。 接下来，使用 `@while` 指令，*while* `$x` 小于 13 时创建网格系统 。 在设置 `width` 的 CSS 规则后，`$x` 增加 1 以避免无限循环。

## Partials 自定义css片段

Sass 中的 Partials 是包含 CSS 代码段的单独的文件。 这些片段可以导入其它 Sass 文件使用。 可以把类似代码放到模块中，以保持代码结构规整且易于管理。

partials 的名称以下划线（`_`）字符开头，这样 Sass 就知道它是 CSS 的一小部分，而不会将其转换为 CSS 文件。 此外，Sass 文件以 `.scss` 文件扩展名结尾。 要将 partial 中的代码放入另一个 Sass 文件中，使用 `@import` 指令。

例如，如果所有 mixins 都保存在名为 “_mixins.scss” 的 partial 中，并且在 “main.scss” 文件中需要它们，下面是使用方法：

```scss
@import 'mixins'
```

**请注意，`import` 语句中不需要下划线——Sass 知道它是 partial**。 将 partial 导入文件后，可以使用所有变量、mixins 和其它代码。

## @extend 继承css样式 好！

Sass 有一个名为 `extend` 的功能，可以很容易地从一个元素中借用 CSS 规则并在另一个元素上重用它们。

例如，下面的 CSS 规则块设置了 `.panel` class 的样式。 它有 `background-color`，`height` 和 `border`。

```scss
.panel{
  background-color: red;
  height: 70px;
  border: 2px solid green;
}
```

现在需要另一个名为 `.big-panel` 的面板。 它具有与 `.panel` 相同的基本属性，但还需要 `width` 和 `font-size`。 可以从 `.panel` 复制并粘贴初始 CSS 规则，但是当添加更多类型的面板时，代码会变得重复。 `extend` 指令是一种重用为一个元素编写的规则的简单方法，可以为另一个元素重用并添加更多规则：

```scss
.big-panel{
  @extend .panel;
  width: 150px;
  font-size: 2em;
}
```

除了新样式之外，`.big-panel` 将具有与 `.panel` 相同的属性。