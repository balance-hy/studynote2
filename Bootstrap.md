# Bootstrap

## Containers 容器

容器是Bootstrap中最基本的布局元素，在使用我们的默认网格系统时是必需的。容器用于包含、填充和（有时）集中其中的内容。虽然容器可以嵌套，但大多数布局不需要嵌套容器。

Bootstrap有三个不同的容器：

- `.container`, which sets a `max-width` at each responsive breakpoint
- `.container-fluid`, which is `width: 100%` at all breakpoints
- `.container-{breakpoint}`, which is `width: 100%` until the specified breakpoint

**其实就是页面大小不同时，容器大小不一致，具体见下表**

|                    | Extra small <576px | Small ≥576px | Medium ≥768px | Large ≥992px | Extra large ≥1200px |
| ------------------ | ------------------ | ------------ | ------------- | ------------ | ------------------- |
| `.container`       | 100%               | 540px        | 720px         | 960px        | 1140px              |
| `.container-sm`    | 100%               | 540px        | 720px         | 960px        | 1140px              |
| `.container-md`    | 100%               | 100%         | 720px         | 960px        | 1140px              |
| `.container-lg`    | 100%               | 100%         | 100%          | 960px        | 1140px              |
| `.container-xl`    | 100%               | 100%         | 100%          | 100%         | 1140px              |
| `.container-fluid` | 100%               | 100%         | 100%          | 100%         | 100%                |

###  网格系统

Bootstrap的网格系统使用一系列容器、行和列来布局和对齐内容。它是使用flexbox构建的，并且是响应式。下面是一个示例，并深入了解网格是如何组合在一起的。

```html
<div class="container">
  <div class="row">
    <div class="col-sm">
      One of three columns
    </div>
    <div class="col-sm">
      One of three columns
    </div>
    <div class="col-sm">
      One of three columns
    </div>
  </div>
</div>
```

![boot1](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202309241228949.PNG)



上面的示例使用我们预定义的网格类可以在小型、中型、大型和特大型设备上创建三个等宽列。这些列位于父容器的页面中心

### 自动列宽

利用栅格断点特性进行排版，可以简化列的大小，而不需要给定显式的列宽，如强制写为：`.col-sm-6`。

`.col-sm-*`代表所占列数，一行是12列。一个例子:

```html
<div class="container">
  <div class="row">
    <div class="col">
      1 of 3
    </div>
    <div class="col-6">
      2 of 3 (更宽-12格中占6格，其它6格另外两列平分)
    </div>
    <div class="col">
      3 of 3
    </div>
  </div>
</div>
```

## 一些问题

问题1：列间有间距吗  行如何控制列间距

问题2：列宽断点怎么定义

## 各式类

### 图像

#### 图像自适应

幸运的是，使用 Bootstrap，我们所需要做的就是将`img-responsive`类添加到您的图像中。这样做，图像应该完全适合页面的宽度。

### 文本

#### 文本居中

现在我们正在使用 Bootstrap，我们可以将标题元素居中以使其看起来更好。我们需要做的就是将类添加`text-center`到我们的`h2`元素中

### 按钮

通常，`button`具有`btn`和`btn-default`类的元素的宽度仅与其包含的文本一样宽。例如：

```html
<button class="btn btn-default">Submit</button>
```

该按钮的宽度只能与单词一样宽`Submit`。

通过使它们成为具有**附加类 的块元素`btn-block`**，您的按钮将拉伸以填充页面的整个水平空间，并且其后面的任何元素将流到块下方的“新行”上。

```html
<button class="btn btn-default btn-block">Submit</button>
```

该按钮将占据 100% 的可用宽度。

#### 按钮颜色(字体颜色类似,text-*)

```html
<!--各种按钮颜色-->
<button type="button" class="btn btn-primary">Primary</button><!--蓝色默认-->
<button type="button" class="btn btn-secondary">Secondary</button><!--次选-->
<button type="button" class="btn btn-success">Success</button><!--成功-->
<button type="button" class="btn btn-danger">Danger</button><!--危险-->
<button type="button" class="btn btn-warning">Warning</button><!--警告-->
<button type="button" class="btn btn-info">Info</button><!--信息-->
<button type="button" class="btn btn-light">Light</button><!--白天-->
<button type="button" class="btn btn-dark">Dark</button><!--黑夜-->
<!--按钮链接样式-->
<button type="button" class="btn btn-link">Link</button>
```

### 表单

文本控件（如 `<input>`、`<select>`、 `<textarea>`）统一采用 `.form-control` 样式进行处理优化，包括常规外观、focus选（点）中状态、尺寸大小等。

### 其他





## Font Awesome

Font Awesome 是一个方便的图标库。这些图标可以是网络字体或矢量图形。**这些图标就像字体一样对待。您可以使用像素指定它们的大小，并且它们将采用其父 HTML 元素的字体大小。**

您可以通过将以下代码添加到 HTML 顶部来将 Font Awesome 包含在任何应用程序中：

```html
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.8.1/css/all.css" integrity="sha384-50oBUHEmvpQ+1lW4y57PTFmhCaXp0ML5d60M1M7uH2+nqUivzIebhndOJK28anvf" crossorigin="anonymous">
```

在这种情况下，我们已经在幕后为您将其添加到此页面。

该`i`元素最初用于使其他元素变为斜体，但现在通常用于图标。您可以将 Font Awesome 类添加到`i`元素以将其变成图标，例如：

```html
<i class="fas fa-info-circle"></i>
```

请注意，该`span`元素也可以与图标一起使用。

```html
<span class="text-danger fas fa-thumbs-up">love:</span>
```

