# CSS

## 选择器

### 类型选择器

有时也叫做“标签名选择器“或者是”元素选择器“，因为它在文档中选择了一个 HTML 标签/元素的缘故。多个相同赋值可合并，逗号分隔

```css
h1,h2,p {
        text-align: center;
}
```

### 全局选择器

全局选择器，是由一个星号（`*`）代指的，它选中了文档中的所有内容（或者是父元素中的所有内容，比如，它紧随在其他元素以及邻代运算符之后的时候）。

下面的示例中，我们已经用全局选择器，移去了所有元素上的外边距。

```css
* {
    margin: 0;
}
```

### 类选择器

类选择器以一个句点（`.`）开头，会选择文档中应用了这个类的所有物件。不同标签类名可以相同

```html
.highlight {
    background-color: yellow;
}


<h1 class="highlight">Class selectors</h1>
<p class="highlight">Gumbo  <strong>endive</strong> gumbo tatsoi.</p>
```

指定应用一个类的特定元素，以`h1`为例

```css
h1.highlight {
    background-color: pink;
}
```

### id选择器

ID 选择器开头为`#`而非句点，不过基本上和类选择器是同种用法。可是在一篇文档中，一个 ID 只会用到一次。它能选中设定了`id`的元素，你可以在 ID 前面加上类型选择器，只指向元素和 ID 都匹配的类。

```css
#one {
    background-color: yellow;
}

h1#heading {
    color: rebeccapurple;
}
```

### 属性是否存在以及值选择器

|     选择器      |              示例               |                             描述                             |
| :-------------: | :-----------------------------: | :----------------------------------------------------------: |
|    `[attr]`     |           `a[title]`            |            匹配带有一个名为***attr***的属性的元素            |
| `[attr=value]`  | `a[href="https://example.com"]` | 匹配带有一个名为***attr***的属性的元素，其值正为***value***  |
| `[attr~=value]` |      `p[class~="special"]`      | 匹配带有一个名为*attr*的属性的元素，其值正为*value*，或者匹配带有一个*attr*属性的元素，其值有一个或者更多，至少有一个和*value*匹配。 |
| `[attr]=value`  |        `div[lang="zh"]`         | 匹配带有一个名为*attr*的属性的元素，其值可正为*value*，或者开始为*value*，后面紧随着一个连字符。 |

### 属性值的子字符串匹配选择器

些选择器让更高级的属性的值的子字符串的匹配变得可行。例如，如果你有`box-warning`和`box-error`类，想把开头为“box-”字符串的每个物件都匹配上的话，你可以用`[class^="box-"]`来把它们两个都选中。

|     选择器      |        示例         |                             描述                             |
| :-------------: | :-----------------: | :----------------------------------------------------------: |
| `[attr^=value]` | `li[class^="box-"]` | 匹配带有一个名为*attr*的属性的元素，其值开头为*value*子字符串。 |
| `[attr$=value]` | `li[class$="-box"]` | 匹配带有一个名为*attr*的属性的元素，其值结尾为*value*子字符串 |
| `[attr*=value]` | `li[class*="box"]`  | 匹配带有一个名为*attr*的属性的元素，其值的字符串中的任何地方，至少出现了一次*value*子字符串。 |

### 伪类和伪元素选择器

伪类是选择器的一种，它用于选择处于特定状态的元素，比如当它们是这一类型的第一个元素时，或者是当鼠标指针悬浮在元素上面的时候。它们表现得会像是你向你的文档的某个部分应用了一个类一样，帮你在你的标记文本中减少多余的类，让你的代码更灵活、更易于维护。示例

```css
:pseudo-class-name

/*鼠标悬停时变色*/
a:hover {
    color:hotpink;
}   
/*鼠标点击时变色*/
a:visited {
    color:hotpink;
} 

a：link ：当链接未被点击时的默认状态
a：visited：当链接被点击后的状态
a：hover： 当鼠标悬停上时的状态
a：active：当鼠标点击时的状态

p:last-of-type { } 选择最后一个 p 元素
p:first-of-type{}选择第一个 p 元素
p:nth-of-type(3){}选择第三个 p 元素
```

#### ::before

`::before` 用来创建一个伪元素，作为已选中元素的第一个子元素。 你可以用它在第一个元素前添加。

```css
p::before{
    content:"Question #";
}
```

在p元素前加了文字

 #### ::after

`::after` 用来创建一个伪元素，作为已选中元素的最后一个子元素。 你可以用它在最后一个图像后添加一个空元素。 如果你设置其 `width` 为和图像相同，如果是两列布局，它会将最后一张图像挤到左边。 现在它居中显示，因为你在 flex 容器上设置了 `justify-content: center`。

#### ::first-letter

定位元素文本内容中的第一个字母

#### :not

`:not` 伪选择器可用于选择所有与给定 CSS 规则不匹配的元素。

```css
div:not(#example) {
  color: red;
}
```

上面选择了所有 `div` 元素没有 `example` 的 `id`。

### 关系选择器

#### 后代选择器

后代选择器——典型用单个空格（" "）字符——组合两个选择器，比如，第二个选择器匹配的元素被选择，如果他们有一个祖先（父亲，父亲的父亲，父亲的父亲的父亲，等等）元素匹配第一个选择器。选择器利用后代组合符被称作后代选择器。

```css
body article p
```



#### 子代关系选择器

子代关系选择器是个大于号（`>`），只会在选择器选中直接子元素的时候匹配。继承关系上更远的后代则不会匹配。例如，只选中作为`<article>`的直接子元素的`<p>`元素：

```css
article > p
```



## Margin

当margin设置后，当前元素无法生效，反而让父级元素的margin变化，此时对当前元素使用padding或其他方法，从而让当前元素正确设置margin。**margin塌陷**

设置两个元素的margin使之相距200px，正常思维每一个元素设100px，但会发现设置后相距仅为100px，此时发生了**margin**合并，取了其中较大值。

当margin设置为负数时：

为了方便理解负值margin，我们引入参考线的定义，参考线就是margin移动的基准点，而margin的值就是移动的数值。
margin的参考线有两类，一类是top、left，它们以外元素作为参考线，另一类是right、bottom，它们以自身作为参考线。
简单点说就是：

top负值就是以包含块(Containing block) 内容区域的上边或者上方相连元素 margin 的下边为参考线;
left负值就是以包含块(Containing block) 内容区域的左边或者左方相连元素 margin 的右边为参考线;
right负值就是以元素本身border的右边为参考线；
bottom负值就是以元素本身border的下边为参考线；

下面这张图很直观地解释了margin的移动，其中红色箭头表示正值时候的移动方向，当margin为负值的时候就反方向移动。黑色实线可视为初始基准点。

![margin](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202309131229930.webp)

就是当margin-top、left为负值的时候与参考线的距离减少，当margin-right、bottom为负值的时候参考线就向左、上面移动。

[margin负值详解](https://zhuanlan.zhihu.com/p/595785843)

圣杯布局[margin-left-100%:详解](https://blog.csdn.net/m0_59132472/article/details/129761215)

## 属性

### opacity 透明度

使用 CSS `opacity` 属性，你可以控制元素的不透明或透明程度。 使用值 `0` 或 0%，元素将完全透明，而在 `1.0` 或 100% 时，元素将像默认情况下一样完全不透明

### rgba 颜色及透明度

`rgba` 函数的工作方式与 `rgb` 函数类似，但在 `0` 到 `1.0` 之间多取一个数字作为 alpha 通道：

```css
rgba(redValue, greenValue, blueValue, alphaValue);
```

### border 边框

`border` 简写属性允许你同时设置边框的宽度、样式和颜色

```css
border: width style color;
```

当然也可以指定某一条边

```css
border-left: width style color;
```

#### border-radius 边角半径

添加值为 `xx px` 的 `border-radius` 属性，使你的图像变得平滑一些。

### box-shadow 元素阴影

box-shadow 属性允许你在元素周围应用一个或多个阴影。 这是基本语法

```css
box-shadow: offsetX offsetY color;
```

下面是 `offsetX` 和 `offsetY` 值的作用：

- `offsetX` 和 `offsetY` 都接受数字值，单位是 `px` 和其他 CSS 单位
- 正 `offsetX` 值将阴影向右移动，负值将它向左移动
- 正 `offsetY` 值将阴影向下移动，负值将它向上移动
- 如果你想要 `offsetX` 和 `offsetY` 其中一个值为零，或两个都为零（`0`），那么你不需要添加单位。 每种浏览器都知道零意味着没有变化。

阴影的高度和宽度由应用阴影的元素的高度和宽度决定。 你也可以使用可选的 `spreadRadius` 值来扩大阴影的范围

#### 阴影边缘

请注意，阴影的边缘是尖锐的。 这是因为 `box-shadow` 属性有一个可选的 `blurRadius` 值：

```css
box-shadow: offsetX offsetY blurRadius color;
```

如果不设置 `blurRadius` 的值，则默认为 `0`，并产生锐利的边缘。 `blurRadius` 的值越大，模糊效果就越大。

#### 阴影范围

如果你想把阴影进一步扩大呢？ 你可以使用可选的 `spreadRadius` 值来做到这一点：

```css
box-shadow: offsetX offsetY blurRadius spreadRadius color;
```

与 `blurRadius` 一样，`spreadRadius` 的值如果没有设置，则默认为 `0`。

### overflow

overflow 属性用于控制内容溢出元素框时显示的方式。

CSS overflow 属性可以控制内容溢出元素框时在对应的元素区间内添加滚动条。

| 值      | 描述                                                     |
| :------ | :------------------------------------------------------- |
| visible | 默认值。内容不会被修剪，会呈现在元素框之外。             |
| hidden  | 内容会被修剪，并且其余内容是不可见的。                   |
| scroll  | 内容会被修剪，但是浏览器会显示滚动条以便查看其余的内容。 |
| auto    | 如果内容被修剪，则浏览器会显示滚动条以便查看其余的内容。 |
| inherit | 规定应该从父元素继承 overflow 属性的值。                 |

**注意:**overflow 属性只工作于指定高度的块元素上。

### filter 视觉效果

定义元素（通常是 <img>）的视觉效果（如模糊和饱和度）

| 滤镜                                             | 描述                                                         |
| :----------------------------------------------- | :----------------------------------------------------------- |
| none                                             | 默认值。规定无效果。                                         |
| blur(px)                                         | 对图像应用模糊效果。较大的值将产生更多的模糊。如果为指定值，则使用 0。 |
| brightness(%)                                    | 调整图像的亮度。0％ 将使图像完全变黑。默认值是 100％，代表原始图像。值超过 100％ 将提供更明亮的结果。 |
| contrast(%)                                      | 调整图像的对比度。0％ 将使图像完全变黑。默认值是 100％，代表原始图像。超过 100％ 的值将提供更具对比度的结果。 |
| drop-shadow(h-shadow v-shadow blur spread color) | 对图像应用阴影效果。                                         |
| grayscale(%)                                     | 将图像转换为灰阶。0% (0) 是默认值，代表原始图像。100％ 将使图像完全变灰（用于黑白图像）。**注释：**不允许负值。 |
| hue-rotate(deg)                                  | 在图像上应用色相旋转。该值定义色环的度数。默认值为 0deg，代表原始图像。**注释：**最大值是 360deg。 |
| invert(%)                                        | 反转图像中的样本。0% (0) 是默认值，代表原始图像。100％将使图像完全反转。**注释：**不允许负值。 |
| opacity(%)                                       | 设置图像的不透明度级别。opacity-level 描述了透明度级别，其中：0% 为完全透明。100% (1) 是默认值，代表原始图像（不透明）。**注释：**不允许负值。**提示：**这个滤镜类似 opacity 属性。 |
| saturate(%)                                      | 设置图像的饱和度。0% (0) will make the image completely un-saturated.100% is default and represents the original image.Values over 100% provides super-saturated results.**注释：**不允许负值。 |
| sepia(%)                                         | 将图像转换为棕褐色。0% (0) 是默认值，代表原始图像。100％ 将使图像完全变为棕褐色。**注释：**不允许负值。 |
| url()                                            | url() 函数接受规定 SVG 滤镜的 XML 文件的位置，并且可以包含指向特定滤镜元素的锚点。实例：filter: url(svg-url#element-id) |

### transform 变换

Transform属性应用于元素的2D或3D转换。这个属性允许你将元素旋转，缩放，移动，倾斜等。

```css
transform: none|transform-functions;
/*倾斜 skew这需要两个参数。 第一个是沿横坐标倾斜的角度，第二个是沿纵坐标倾斜的角度。*/
/*沿 x 轴倾斜 0deg 沿 y 轴倾斜 44deg。*/
transform:skew(0deg,44deg);
```

### box-sizing

box-sizing 属性定义如何计算一个元素的总宽度和总高度，主要设置是否需要加上内边距(padding)和边框等。

例如，假如您需要并排放置两个带边框的框，可通过将 box-sizing 设置为 "border-box"。这样就可以让浏览器呈现出带有指定宽度和高度的框，并把边框和内边距放入框中。

```css
box-sizing: content-box|border-box|inherit:
```



| 值          | 说明                                                         |
| :---------- | :----------------------------------------------------------- |
| content-box | 默认值。如果你设置一个元素的宽为 100px，那么这个元素的内容区会有 100px 宽，并且任何边框和内边距的宽度都会被增加到最后绘制出来的元素宽度中。 |
| border-box  | 告诉浏览器：你想要设置的边框和内边距的值是包含在 width 内的。也就是说，如果你将一个元素的 width 设为 100px，那么这 100px 会包含它的 border 和 padding，内容区的实际宽度是 width 减 去(border + padding) 的值。大多数情况下，这使得我们更容易地设定一个元素的宽高。 **注：**border-box 不包含 margin。 |
| inherit     | 指定 box-sizing 属性的值，应该从父元素继承                   |



### letter-spacing 文字间距

`letter-spacing` 属性可用于调整元素中文本的每个字符之间的间距。

### line-height 行高

`line-height:xx;`

### column-width列宽

可以使用 `column-width` 属性在不使用网格的情况下在元素内创建列

### aspect-ratio 纵横比

```css
aspect-ratio:35/4;
```

### cursor 光标样式

```css
cursor:pointer /*光标变成一只手*/
```

### text-align 文本水平对齐方式

| 值      | 描述                                       |
| :------ | :----------------------------------------- |
| left    | 把文本排列到左边。默认值：由浏览器决定。   |
| right   | 把文本排列到右边。                         |
| center  | 把文本排列到中间。                         |
| justify | 实现两端对齐文本效果。                     |
| inherit | 规定应该从父元素继承 text-align 属性的值。 |

### position 定位

[position详解](https://blog.csdn.net/m0_64460142/article/details/124048196)

position 属性指定了元素的定位类型。

position 属性的五个值：

- **static**

  HTML 元素的默认值，即没有定位，遵循正常的文档流对象。

- **relative**

  相对定位元素的定位是相对其正常位置。

- **fixed**

  元素的位置相对于浏览器窗口是固定位置。即使窗口是滚动的它也不会移动：

- **absolute**

  绝对定位的元素的位置相对于最近的已定位父元素，如果元素没有已定位的父元素，那么它的位置相对于<html>:

- **sticky**

  sticky 英文字面意思是粘，粘贴，所以可以把它称之为粘性定位。

  *position: sticky;* 基于用户的滚动位置来定位。

  粘性定位的元素是依赖于用户的滚动，在 *position:relative* 与 *position:fixed* 定位之间切换。

  它的行为就像 *position:relative;* 而当页面滚动超出目标区域时，它的表现就像 *position:fixed;*，它会固定在目标位置。

  元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位

### list-style 列表样式

```css
list-style:none; /*删除列表样式*/
```

| 值                  | 描述                                                         |
| :------------------ | :----------------------------------------------------------- |
| list-style-type     | 设置列表项标记的类型。参阅：[list-style-type](https://www.runoob.com/css/pr-list-style-type.html) 中可能的值。 |
| list-style-position | 设置在何处放置列表项标记。参阅：[list-style-position](https://www.runoob.com/css/pr-list-style-position.html) 中可能的值。 |
| list-style-image    | 使用图像来替换列表项的标记。参阅：[list-style-image](https://www.runoob.com/css/pr-list-style-image.html) 中可能的值。 |
| initial             | 将这个属性设置为默认值。参阅：[initial](https://www.runoob.com/cssref/css-initial.html) 中可能的值。 |
| inherit             | 规定应该从父元素继承 list-style 属性的值。参阅：[inherit](https://www.runoob.com/cssref/css-inherit.html) 中可能的值。 |

### scroll-behavior 点击按钮页内跳转样式

```css
scroll-behavior: auto; /*无动画*/
scroll-behavior: smooth; /*有动画*/
```

### @media 规则

@media 规则在媒体查询中用于为不同的媒体类型/设备应用不同的样式。

媒体查询可用于检查许多事情，诸如：

- 视口的宽度和高度
- 设备的宽度和高度
- 方向（手机或平板电脑处于横屏还是竖屏模式？）
- 分辨率

```css
/*如果浏览器窗口的宽度为 600px 或更小时，把 <body> 元素的背景颜色更改为“浅蓝色”：*/
@media only screen and (max-width: 600px) {
  body {
    background-color: lightblue;
  }
}
/*and 运算符用于查询两个媒体条件*/
```



### clip 裁剪

如果图像大于包含它的元素，会发生什么？-clip属性，让你指定一个绝对定位的元素，该尺寸应该是可见的，该元素被剪裁成这种形状并显示。

**注意：:** 如果先有"overflow：visible"，clip属性不起作用。

rect里面参数遵循（`top, right, bottom, left`）

```css
clip:rect(0px,60px,200px,0px);
那么，这里面的上右下左怎么理解呢，其实是这样的，top right bottom left分别指最终剪裁可见区域的上边，右边，下边与左边。而所有的数值都表示位置，且是相对于原始元素的左上角而言的。例如：
```

#### clip-path 形状

`clip-path` 属性决定了 `clip` 属性应该采用的形状。 设置 `clip-path` 属性的值为 `inset(xx%)`，在元素内剪切形成一个矩形

### Overflow 内容溢出

CSS overflow 属性可以控制内容溢出元素框时在对应的元素区间内添加滚动条。

overflow属性有以下值：

| 值      | 描述                                                     |
| :------ | :------------------------------------------------------- |
| visible | 默认值。内容不会被修剪，会呈现在元素框之外。             |
| hidden  | 内容会被修剪，并且其余内容是不可见的。                   |
| scroll  | 内容会被修剪，但是浏览器会显示滚动条以便查看其余的内容。 |
| auto    | 如果内容被修剪，则浏览器会显示滚动条以便查看其余的内容。 |
| inherit | 规定应该从父元素继承 overflow 属性的值。                 |

**注意:**overflow 属性只工作于指定高度的块元素上。

### white-space 处理元素内空白

white-space属性指定元素内的空白怎样处理。

| 值       | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| normal   | 默认。空白会被浏览器忽略。                                   |
| pre      | 空白会被浏览器保留。其行为方式类似 HTML 中的 <pre> 标签。    |
| nowrap   | 文本不会换行，文本会在在同一行上继续，直到遇到 <br> 标签为止。 |
| pre-wrap | 保留空白符序列，但是正常地进行换行。                         |
| pre-line | 合并空白符序列，但是保留换行符。                             |
| inherit  | 规定应该从父元素继承 white-space 属性的值。                  |

### z-index z顺序

z-index属性指定了元素及其子元素的【z顺序】，而【z顺序】可以决定当元素发生覆盖的时候，哪个元素在上面。通常一个较大z-index值的元素会覆盖较低的那一个

### !important 防止属性覆盖

为了不必反复仔细检查你有没有覆盖之前的属性，你可以使用 `!important` 关键字来确保始终应用这些属性，而不考虑其顺序或特殊性。

```css
border: 1px !important ;
```

### border-collapse 表格边框

**`border-collapse`** CSS 属性是用来决定表格的边框是分开的还是合并的。在分隔模式下，相邻的单元格都拥有独立的边框。在合并模式下，相邻单元格共享边框

通俗的说就是每一个单元格是否有独立的边框

```css
border-collapse: collapse;/*合并，无独立*/
border-collapse: separate;/*分开，有独立*/
```

### vertical-align 垂直对齐

vertical-align 属性设置一个元素的垂直对齐方式。

该属性定义行内元素的基线相对于该元素所在行的基线的垂直对齐。允许指定负长度值和百分比值。这会使元素降低而不是升高。在表单元格中，这个属性会设置单元格框中的单元格内容的对齐方式。

| 值          | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| baseline    | 默认。元素放置在父元素的基线上。                             |
| sub         | 垂直对齐文本的下标。                                         |
| super       | 垂直对齐文本的上标                                           |
| top         | 把元素的顶端与行中最高元素的顶端对齐                         |
| text-top    | 把元素的顶端与父元素字体的顶端对齐                           |
| middle      | 把此元素放置在父元素的中部。                                 |
| bottom      | 使元素及其后代元素的底部与整行的底部对齐。                   |
| text-bottom | 把元素的底端与父元素字体的底端对齐。                         |
| length      | 将元素升高或降低指定的高度，可以是负数。                     |
| %           | 使用 "line-height" 属性的百分比值来排列此元素。允许使用负值。 |
| inherit     | 规定应该从父元素继承 vertical-align 属性的值。               |

### float 浮动

float 属性定义元素在哪个方向浮动。以往这个属性总应用于图像，使**文本**围绕在**图像**周围，不过在 CSS 中，任何元素都可以浮动。浮动元素会生成一个块级框，而不论它本身是何种元素。

| 属性                                                       | 描述                               | 值                           |
| :--------------------------------------------------------- | :--------------------------------- | :--------------------------- |
| [clear](https://www.runoob.com/cssref/pr-class-clear.html) | 指定不允许元素周围有浮动元素。     | left right both none inherit |
| [float](https://www.runoob.com/cssref/pr-class-float.html) | 指定一个盒子（元素）是否可以浮动。 | left right none inherit      |

一般，现在中文圈流传的表述是：

clear语法：

clear : none | left | right | both

取值：

- none : 默认值。允许两边都可以有浮动对象
- left : 不允许左边有浮动对象
- right : 不允许右边有浮动对象
- both : 不允许有浮动对象

### text-decoration 文本修饰(去下划线)

用的最多的地方，可能就是a标签了，用来将a标签文本的下划线去掉，我们通常设置：

```css
a{text-decoration:none}
```

然而text-decoration其实是三个属性的缩写:

* text-decoration-line

  定义：用来规定文本修饰要使用的线条类型。

  取值：大概就是：none，underline，line-through(规定文本中间将显示一条线)等等。

* text-decoration-color

  定义：用来规定文本修饰（下划线 underline、上划线 overline、中划线 line-through）的颜色。

  取值：所有颜色表示法。

* text-decoration-style

  定义：用来规定线条如何显示。

  取值：solid和double和dotted和dashed等等。

这三种属性可以简写，如：`text-decoration: underline red solid`。

### gradient 渐变

CSS 中的渐变是一种跨元素距离在颜色之间过渡的方法。 它们应用于 `background` 属性，语法如下所示：

```css
gradient-type(
  (direction,)/*控制渐变方向，可选*/
  color1 (xx%),
  color2
) 
/*xx%控制渐变结束位置*/
linear-gradient(
  var(--first-color) 0%,
  var(--first-color) 40%,/*0-40 firstcolor 40-80 second-color*/
  var(--second-color) 40%,
  var(--second-color) 80%
);
background: linear-gradient(45deg, rgb(118, 201, 255), rgb(247, 255, 222));
```

在示例中，`color1` 在顶部是实心的，`color2` 在底部是实心的，并且在它们之间均匀地从一个过渡到下一个。

### object-fit 

指定元素的内容应该如何去适应指定容器的高度与宽度。

| 值         | 描述                                                         |
| :--------- | :----------------------------------------------------------- |
| fill       | 默认，不保证保持原有的比例，内容拉伸填充整个内容容器。       |
| contain    | 保持原有尺寸比例。内容被缩放。                               |
| cover      | 保持原有尺寸比例。但部分内容可能被剪切。                     |
| none       | 保留原有元素内容的长度和宽度，也就是说内容不会被重置。       |
| scale-down | 保持原有尺寸比例。内容的尺寸与 none 或 contain 中的一个相同，取决于它们两个之间谁得到的对象尺寸会更小一些。 |
| initial    | 设置为默认值                                                 |
| inherit    | 从该元素的父元素继承属性。                                   |

### rem em px 区别

**三者区别**：
在css中单位长度用的最多的是px、em、rem，这三个的区别是：

- px是固定的像素，一旦设置了就无法因为适应页面大小而改变。
- em和rem相对于px更具有灵活性，他们是相对长度单位，意思是长度不是定死了的，更适用于响应式布局。
- em是相对于其父元素来设置字体大小的，一般都是以`<body>`的“font-size”为基准。这样就会存在一个问题，进行任何元素设置，都有可能需要知道他父元素的大小。而Rem是相对于根元素`<html>`，这样就意味着，我们只需要在根元素确定一个参考值。

总之：对于em和rem的区别一句话概括：

**em相对于父元素，rem相对于根元素**

### vertical-align

>https://zhuanlan.zhihu.com/p/28626505?utm_medium=social

主要用于内联元素垂直方向上对齐

## 变量

### 声明及使用

```css
--variable-name: value;
var(--variable-name(, fallback-value))/*后一项为可选项，当变量出现问题，使用它*/
/*示例*/
--building-color1: #999;
background-color:var(--building-color1);
```

### 作用域

在一般的选择器中（例如class为.bb1）中声明的变量不会级联到 `.bb2` 和 `.bb3` 兄弟元素。 这就是 CSS 的工作原理。 因此，变量通常在 `:root` 选择器中声明。 这是 CSS 中最高级别的选择器；放在里面的变量将在任何地方都可用。

```css
:root {
  --building-color1: #aa80ff;
  --building-color2: #66cc99;
  --building-color3: #cc6699;
  --building-color4: #538cc6;
}
```

## 函数

### repeat() 重复

CSS `repeat()` 函数用于重复一个值，而不是手动写出它，这对网格布局很有帮助。

```css
grid-template-columns:repeat(20, 200px);/*创建 20 列，每列 200px 宽*/
```



### minmax() 范围

使用 `minmax` 函数使你的列在任何设备上都能响应。 `minmax` 函数有两个参数，第一个是最小值，第二个是最大值。 这些值可以是长度、百分比、`fr`，甚至是像 `max-content` 这样的关键字

```css
grid-template-columns:minmax(2rem, 1fr) minmax(min-content, 94rem) minmax(2rem, 1fr);
```

### calc(expression) 计算

calc() 函数用于动态计算长度值。

- 需要注意的是，运算符前后都需要保留一个空格，例如：`width: calc(100% - 10px)`；
- 任何长度值都可以使用calc()函数进行计算；
- calc()函数支持 "+", "-", "*", "/" 运算；
- calc()函数使用标准的数学运算优先级规则；

## 布局

### grid

```css
display:grid;
```

[grid 详解](https://www.jianshu.com/p/3762f214cd6f)

#### grid-template-columns 列数

fr:剩余空间的分数，例如`grid-template-columns：1fr 1fr;`代表两列，每列占剩余空间一半

#### gap 间距

```css
gap:3rem;/*行间距*/
gap:3rem 1rem;/*行间距 列间距*/
/*等价于*/
row-gap:3rem;
column-gap:1rem;
```

#### grid-column

用来指定item的具体位置，根据在哪根网格线。它是 `grid-column-start` 和 `grid-column-end` 的简写。 `grid-column` 属性告诉网格项从哪条网格线开始和结束。

```css
grid-column-start:1;
grid-column-end:3;
grid-column:1/3;/*上面属性的简写*/
```

请记住，`grid-column` 属性确定元素开始和结束的列。 有时你不确定网格将有多少列，但你希望元素停在最后一列。 为此，你可以使用 `-1` 作为结束列。

```css
grid-column:1 / -1;/*元素跨越网格的整个宽度*/
```

#### grid-auto-flow

划分网格以后，容器的子元素会按照顺序，自动放置在每一个网格。默认的放置顺序是"先行后列".即先填满第一行，再开始放入第二行(就是子元素的排放顺序)

```css
grid-auto-flow:row;/*先填满第一行*/
grid-auto-flow:column;/*先填满第一列*/
grid-auto-flow:row dense;/*dense 值允许算法回溯并用较小的项目填充网格中的空白，这可能导致项目出现乱序。*/
```

row dense属性值表示换行时留下的空间可以由下面的元素填补上去

#### grid-auto-columns 新列宽

用来设置多出来的项目宽和高

```css
grid-auto-columns:1fr;/*默认新列宽为1fr*/
grid-auto-rows
```

#### place-items 元素位置

用于同时设置 `align-items` 和 `justify-items` 的值

`place-items` 属性采用一个或两个值。 如果提供了一个值，则它同时用于 `align-items` 和 `justify-items` 属性。 如果提供了两个值，则第一个值用于 `align-items` 属性，第二个值用于 `justify-items` 属性。

### flex

```css
display:flex;
```

![flex](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202309081058516.webp)

[详解](https://zhuanlan.zhihu.com/p/440852043)

控制容器内元素间隔和对齐方式

```css
display:flex;
```

Flexbox 可以被认为有两个轴，主轴和交叉轴。 主轴由 `flex-direction` 属性决定，有四个可选值：

- `row`（默认值）：主轴为水平方向，起点在左端。
- `row-reverse`：主轴为水平方向，起点在右端。
- `column`：主轴为垂直方向，起点在上沿。
- `column-reverse`：主轴为垂直方向，起点在下沿。

**注意**：轴和方向将因文字方向而异。 上面的值是左至右的文本方向。

#### flex-wrap 项目排列

`flex-wrap` 属性确定当 flex 容器太小时项目的排列方式。 将此属性设置为 `wrap` 将允许项目换行到下一行/列（取决于主轴）。 `nowrap`（默认值）将阻止项目换行，此时项目可能会收缩以自适应或溢出。

#### justify-content 内容对齐

`justify-content` 属性决定了 flex 容器中的项目如何沿主轴对齐，从而影响它们的位置和周围的空间。

```css
justify-content:space-evenly; /*均匀分布*/
justify-content:space-between：/*两端对齐*/
justify-content:flex-end;/*末端对齐*/
justify-content:space-around;/*弹性项目平均分布在该行上，两边留有一半的间隔空间。如果剩余空间为负或者只有一个弹性项，则该值等同于center*/
```

#### justify-items

沿行轴对齐子元素。

#### align-items

`align-items` 属性沿列轴对齐子元素。 在这种情况下，将 `flex-direction` 设置为 `row`，横轴将是垂直的。

它可取5个值：具体的对齐方式与交叉轴的方向有关，这里以交叉轴从上到下为例

|   align-items   |                        取值                        |
| :-------------: | :------------------------------------------------: |
| stretch（默认） | 如果项目未设置高度或设为auto，将占满整个容器的高度 |
|   flex-start    |                  交叉轴的起点对齐                  |
|    flex-end     |                  交叉轴的终点对齐                  |
|     center      |                  交叉轴的中点对齐                  |
|    baseline     |             项目的第一行文字的基线对齐             |



#### object-fit 内容纵横比

为 `.gallery img` 选择器提供一个 `object-fit` 属性，并将其设置为 `cover`。 这将告诉图像填充 `img` 容器，同时保持横纵比，以便裁剪到适合。

请注意你的一些图像是如何失真的。 这是因为图像具有不同的纵横比。 你可以使用 `object-fit` 属性来确定图像的行为方式，而不是单独设置每个纵横比

#### gap 内容间隙

`gap` 是设置间隙的 CSS 简写形式（也叫作 gutters），是行和列之间的空白。 `gap` 属性以及 `row-gap` 和 `column-gap` 子属性用来设置 flex、grid 和多列布局的间隙。 可以将此属性应用到容器元素。



## 动画

[动画详解](https://www.w3school.com.cn/css/css3_animations.asp)

### transform-origin 变换原点

`transform-origin`属性用于设置应用CSS变换点。 例如，当执行 `rotate`时，`transform-origin`决定了元素围绕哪一点旋转。

```css
transform:rotate(60deg);/*旋转60度，当设置了原点就会绕原点旋转*/
transform-origin:50% 0%;/*左侧偏移50% 顶部偏移0%*/
```

### transition 过渡

通常当 CSS 的属性值更改后，浏览器会立即更新相应的样式，例如当鼠标悬停在元素上时，通过 :hover 选择器定义的样式会立即应用在元素上。在 CSS3 中加入了一项过渡功能，通过该功能您可以将元素从一种样式在指定时间内平滑的过渡到另一种样式，类似于简单的动画，但无需借助 flash 或 JavaScript。

- transition-property：设置元素中参与过渡的属性；

- transition-duration：设置元素过渡的持续时间；

- transition-timing-function：设置元素过渡的动画类型；

- transition-delay：设置过渡效果延迟的时间，默认为 0；

- transition：简写属性，用于同时设置上面的四个过渡属性。

- ```css
  transition: transition-property transition-duration transition-timing-function transition-delay;
  ```

  

### @keyframes 规则

如果您在 `@keyframes` 规则中指定了 CSS 样式，动画将在特定时间逐渐从当前样式更改为新样式。

要使动画生效，必须将动画绑定到某个元素。

下面的例子将 "example" 动画绑定到 `<div>` 元素。动画将持续 4 秒钟，同时将` <div>` 元素的背景颜色从 "red" 逐渐改为 "yellow"：

```css
/* 动画代码 */
@keyframes example {
  from {background-color: red;}
  to {background-color: yellow;}
}
/* 向此元素应用动画效果 */
div {
  width: 100px;
  height: 100px;
  background-color: red;
  animation-name: example;
  animation-duration: 4s;
}
```



**注意：**`animation-duration` 属性定义需要多长时间才能完成动画。如果未指定 `animation-duration` 属性，则动画不会发生，因为默认值是 0s（0秒）。

在上面的例子中，通过使用关键字 "from" 和 "to"（代表 0％（开始）和 100％（完成）），我们设置了样式何时改变。

您也可以使用百分比值。通过使用百分比，您可以根据需要添加任意多个样式更改。

下面的例子将在动画完成 25％，完成 50％ 以及动画完成 100％ 时更改 `<div> `元素的背景颜色：

```css
/* 动画代码 */
@keyframes example {
  0%   {background-color: red;}
  25%  {background-color: yellow;}
  50%  {background-color: blue;}
  100% {background-color: green;}
}
```

### animation-delay 延迟动画

`animation-delay` 属性规定动画开始的延迟时间。

下面的例子在开始动画前有 2 秒的延迟：

```css
div {
  width: 100px;
  height: 100px;
  position: relative;
  background-color: red;
  animation-name: example;
  animation-duration: 4s;
  animation-delay: 2s;
}
```

负值也是允许的。如果使用负值，则动画将开始播放，如同已播放 N 秒。

### animation-iteration-count 动画应运行次数

```css
animation-iteration-count: 3;/*在停止前把动画运行 3 次：*/
/*使用值 "infinite" 使动画永远持续下去：*/
animation-iteration-count: infinite;
```

### animation-direction 反向或交替运行动画

`animation-direction` 属性指定是向前播放、向后播放还是交替播放动画。

`animation-direction` 属性可接受以下值：

- `normal` - 动画正常播放（向前）。默认值
- `reverse` - 动画以反方向播放（向后）
- `alternate` - 动画先向前播放，然后向后
- `alternate-reverse` - 动画先向后播放，然后向前

### animation-timing-function 动画速度

`animation-timing-function` 属性规定动画的速度曲线。

`animation-timing-function` 属性可接受以下值：

- `ease` - 指定从慢速开始，然后加快，然后缓慢结束的动画（默认）
- `linear` - 规定从开始到结束的速度相同的动画
- `ease-in` - 规定慢速开始的动画
- `ease-out` - 规定慢速结束的动画
- `ease-in-out` - 指定开始和结束较慢的动画
- `cubic-bezier(*n*,*n*,*n*,*n*)` - 运行您在三次贝塞尔函数中定义自己的值

### animation-fill-mode 动画填充帧

CSS 动画不会在第一个关键帧播放之前或在最后一个关键帧播放之后影响元素。`animation-fill-mode` 属性能够覆盖这种行为。

在不播放动画时（在开始之前，结束之后，或两者都结束时），`animation-fill-mode` 属性规定目标元素的样式。

animation-fill-mode 属性可接受以下值：

- `none` - 默认值。动画在执行之前或之后不会对元素应用任何样式。
- `forwards` - 元素将保留由最后一个关键帧设置的样式值（依赖 animation-direction 和 animation-iteration-count）。
- `backwards` - 元素将获取由第一个关键帧设置的样式值（取决于 animation-direction），并在动画延迟期间保留该值。
- `both` - 动画会同时遵循向前和向后的规则，从而在两个方向上扩展动画属性。
