---
title: CSS笔记
date: 2023-11-04 13:05:17
tags:
---
# CSS
参考：[Client-Side Web Development](https://info340.github.io/css.html)
## 一、覆盖规则

同级的选择器的同一个属性，谁最后渲染谁生效；

不同级的选择器，最具体的（比如#id比.class具体）优先级最高。

## 二、 复合选择器

### 多个选择器组合

```css
/* can also include class or id selectors */
/* this rule applies to h2 elements, "menu" classed elements, and the
   #sidebar element */
h2, .menu, #sidebar {
    background-color: gray;
}
```

> since later rules take precedence earlier ones, you can use a group select to apply a property to multiple different elements, then include additional rules to add variations.

### 组合选择器

```css
/* Selects only p elements that have class="alert"
   Other p elements and "alert" classed elements not affected */
p.alert {
  color: red;
}

/* Selects only h1 elements that have id="title" */
/* Note that this is redundant, since only one element can have the id! */
h1#title {
  color: purple;
}

/* Selects elements that have class "alert" AND class "success" */
.alert.success {
  color: green;
  font-size: larger;
}

/* And can combine with group selector */
/* applies to <p class="highlighted"> and <li class="selected"> */
p.highlighted, li.marked {
  background-color: yellow;
}
```



### 后代选择器

在父选择器后加空格。所有直接和间接的后代都生效。

```html
<header>
   <h1>Welcome to the page</h1>
   <p>I am a special paragraph</p>
</header>
<section>
   <p>some other paragraph</p>
</section>
```

```css
/*
  Selects p elements that exist within header elements
  Other p elements will not be affected
 */
header p {
    /* ... */
}
```

​	如果嵌套大于3层时，建议改用.class选择器。

### 子类选择器

只会选择直系的子元素。

```css
/* Selects paragraph "Body content" (immediate child of body),
   not paragraph "Section content" (immediate child of section) */
body > p {
  color: blue;
}
```



### 伪类选择器

在基本选择器后加 `:` 。

```css
/* style for unvisited links */
a:link { /*...*/ }

/* style for visited links */
a:visited { /*...*/ }

/* style for links the user is hovering over with the mouse */
a:hover { /*...*/ }

/* style for links that have keyboard focus */
a:focus { /*...*/ }

/* style for links as they are being 'activated' (clicked) */
a:active { /*...*/ }
```

记住：

`a:hover` 必须跟在 `a:link` 之后；

`a:visited` 和 `a:active`必须跟在 `a:hover` 之后；

为了支持只用键盘的人，请同时使用  `a:hover` 和 `a:focus`

> Note that there are [many additional pseudo-classes](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes), including ones that consider specific element attributes (e.g., if a checkbox is `:checked`) or where an element is located within its parent (e.g., if it is the `:first` or `:last-child`, which can be useful for styling lists).

#### nth-* 选择器

`nth-child`  and `nth-of-type`

区别：

```css
/* 只选中第二个p元素 */
p:nth-of-type(2) {
    color:red;
}

/* 只选中第二个元素，但如果第二个元素不是p，那么就不会生效 */
p:nth-child(2) {
    color:red;
}
```

[这里](https://css-tricks.com/the-difference-between-nth-child-and-nth-of-type/)建议使用 `nth-child()` 的时候结合后代选择器使用，而不要说明具体对应的类：

```css
dl :nth-child(2) {  } /* is better than */
dd:nth-child(2) {  } /* this */
```



>  don’t forget about the awesome cousins `:first-of-type`, `:last-of-type`, `:nth-last-of-type` and `:only-of-type`. [Learn more here.](https://css-tricks.com/pseudo-class-selectors/)



The `nth-*` selectors also support the keywords `odd` and `even`, which is really useful for styling tables:

```css
/* Give every-other row a light-gray background */
tr:nth-of-type(even) {
  background-color:#eee;
}
```

### 属性选择器

最经常用来给表格进行美化。

```css
/* select <input type="checkbox"> that have the "checked" state */
input[type=checkbox]:checked {
    color: green;
}
```

It is also possible to select attributes that only “partially” match a particular value; see the documentation for details.



## 三、属性值

### 单位&大小

字体尽量使用相对单位，布局参数可能为绝对单位，但相对单位是最好的：

| Unit       | Meaning                                                      |
| ---------- | ------------------------------------------------------------ |
| `em`       | Relative to the **current** element’s font-size. Although originally a [typographic measurement](http://en.wikipedia.org/wiki/Em_(typography)), this unit will **not** change based on `font-family`. |
| `%`        | Relative to the **parent** element’s font-size *or* dimension. For font-size, use `em` instead (e.g., `1.5em` is `150%` the parent font-size). |
| `rem`      | Relative to the **root** element’s font-size (i.e., the `font-size` of the root `html` or `body` element). This will often be more consistent than `em`. |
| `vw`, `vh` | Relative to the **viewport** (e.g., the browser window). Represents 1% of the viewport width and height, respectively. This unit is not supported [by older browsers](http://caniuse.com/#search=vw). |

一般默认的em是16px，即 `1em` 和 `1rem` 等于 `16px`。

### 颜色

有141种内置的颜色，见[predefined color names](http://www.w3schools.com/cssref/css_colornames.asp)。

```css
p {
   color: mediumpurple;
}

p {
   color: rgb(147, 112, 219); /* medium purple */
}

p {
   background-color: rgba(0,0,0,0.5); /* semi-transparent black */
}

p {
   color: #9370db; /* medium purple */
}
```

- 历史趣闻：The list of CSS color names also has a [fascinating history](http://arstechnica.com/information-technology/2015/10/tomato-versus-ff6347-the-tragicomic-history-of-css-color-names/).

### 字体&图标

```css
p {
    font-family: 'Helvetica Nue', 'Helvetica', 'Arial', sans-serif;
}
```

使用font-family属性指定字体，注意最后一个字体必须为通用字体，These are a list of “categories” that the browser can draw upon even if the computer doesn’t have any common fonts available. In pracice, the most common generic families used are `serif` (fonts with [serifs](https://en.wikipedia.org/wiki/Serif), e.g., “Times”), `sans-serif` (fonts *without* serifs, e.g., “Arial”), and `monospace` (fonts with [equal width characters](https://en.wikipedia.org/wiki/Monospaced_font), e.g., “Courier”).

你也可以在自己的网页上包含特殊的字体，以防客户没有这种字体。比如，使用 Google fonts:

```html
<head>
    <!-- ... -->

    <!-- load stylesheet with font first so it is available -->
    <link href="https://fonts.googleapis.com/css?family=Encode+Sans" rel="stylesheet">

    <!-- load stylesheet next -->
    <link href="css/style.css" rel="stylesheet">
</head>
```

```css
body {
    font-family: 'Encode Sans', sans-serif; /* can now use Encode Sans */
}
```

**注意：**如果你希望在使用Google Fonts的同时使用Bold，Italic等类型，你需要在引用里额外说明，否则不会生效：

```html
<!-- includes normal (400) and bold (700) weights -->
<link href="https://fonts.googleapis.com/css?family=Encode+Sans:400,700" rel="stylesheet">
```



你也可以用类似的方法引入图标，比如font awesome：

```html
<link href="https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css" rel="stylesheet">
```

> This will load the stylesheet from a [Content Delivery Network (CDN)](https://en.wikipedia.org/wiki/Content_delivery_network), which is a web service intended to quickly serve files commonly used by multiple websites. In particular, browsers will *cache* (save) copies of these files locally, so that when you visit a different website (or the same website for a second time), the file will already be downloaded. We will load most of the CSS and JavaScript frameworks used in this class from a CDN.

使用font awesome：

```html
<i class="fa fa-universal-access" aria-hidden="true"></i>
```

**记得加上 `aria-hidden="true"` 来增加Accessibility，使屏幕阅读器不会读出这些图标奇怪的名字！**



### 背景&图片

插入背景图片：

```css
header {
    background-image: url('../img/page-banner.png');
}
```

关于背景，有一堆属性：

```css
header {
    background-image: url('../img/page-banner.png');
    background-position: center top; /* align to center top */
    background-size: cover; /* stretch so element is filled; preserves ratio (img may be cropped) */
    background-repeat: no-repeat; /* don't repeat */
    background-attachment: fixed; /* stay still when window scrolls */
    background-color: beige; /* can still have this for anything the image doesn't cover
                                (or for transparent images) */
}
```

简写：

```css
header {
    background: url('../img/page-banner.png') top center / cover no-repeat fixed beige;
}
```

- The `background-position` and `background-size` are separated by a `/` since they both can have more than one value.

- You can include some or all of the available background properties in the shorthand. Unlike most shorthand properties, the `background` properties can go in any order (though the above is recommended).

注意，简写会用它所包含的属性覆写掉之前定义过的对应属性。

`background` 属性也支持使用逗号隔开的多层背景（[multiple backgrounds](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Background_and_Borders/Using_CSS_multiple_backgrounds)），你可以利用这个特点来重叠透明背景，就像Photoshop那样。



## 四、布局

以左上角为原点，元素依次排列。遵循下列三个概念：

- Display：确定元素怎样共享水平空间
- Position：调整元素的位置
- Box-model：决定元素占据的2维空间，以及元素之间的间隔空间

### Display

- 行内元素
- 块元素

可以使用 `display` 属性控制元素成为行内元素还是块元素：

```css
.inlined {
  display: inline;
}
```

```html
<!-- this will produce the same result as using <span> elements -->
<div class="inlined">Inline element</div>
<div class="inlined">Other inline element</div>
```



如果你想将某个元素从页面中移除：

```css
.hidden {
    display: none;
}
```



你也可以设置 `display: inline-block` ，这个属性让元素表现为行内元素，但允许元素使用 `width` ，`height` 等属性，就像块元素一样。

> This is particularly useful for making things like lists (`<ul>`) appear “inline”.



### 盒子模型

所有元素都像是被装在一个盒子里一样，这个盒子包括4个部分：

- 内容
- padding
- border
- margin

你也可以单独设置 `height` 和 `width` 属性，但是建议只设置其中一个，以免设置不当使内容被裁剪（由 `overflow` 属性控制）。你也可以设置 `min-width` ，`min-height`，`max-width`，`max-height` 属性来指定最大最小值。

#### Padding

padding是内容和border之间的间距。

```css
/* specify each side individually */
div {
  padding-top: 1em;
  padding-bottom: 1em;
  padding-left: 2em;
  padding-right: 0; /* no units needed on 0 */
}

/* specify one value for all sides at once */
div {
  padding: 1.5em;
}

/* specify one value for top/bottom (first)
   and one for left/right (second) */
div {
  padding: 1em 2em;
}
```



#### Border

边框有许多属性，比如 width、color、radius、style等等。

```css
.boxed {
   border: 2px dashed black; /* border on all sides */
}

.underlined {
   border-bottom: 1px solid red; /* border one side */
}

.something { /* control border properties separately */
   border-top-width: 4px;
   border-top-color: blue;
   border-top-style: dotted;
   border-radius: 4px; /* rounded corners! */
}
```



#### Margin

Margin决定了当前盒子和旁边其他盒子之间的间距，定义margin的方式和padding非常相似。

**注意：** 相近元素的Margin通常会互相 [collapse](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing) (overlap)，比如上方元素的 `margin-bottom` 会和下方元素的 `margin-top` 重叠，取二者之间更大的那个作为它们之间的间距。



#### 盒子大小

你盒子的大小应该为：

width + padding + border = actual visible/rendered width of an element’s box

height + padding + border = actual visible/rendered height of an element’s box

例：

```css
.my-box {
    width: 100px;
    padding: 10px; /* includes both left and right */
}
```

上述盒子的宽度为120px。

当使用响应式设计的时候，这种计算方法非常不方便，我们可以设置 `box-sizing` 属性为 `border-bpx` 来 “Reset” 所有元素，使得 `width` 属性包括 padding 和 border。

```css
html {
  box-sizing: border-box;
}
*, *:before, *:after {
  box-sizing: inherit;
}
```

见：https://css-tricks.com/box-sizing/



### Position

display只能改变盒子的形状，要改变盒子的位置，使用position属性。

position有4个数值：

- `static` ：默认数值，就是默认的布局。此时，`top`、`bottom` 、`left` 和 `right` 属性不起作用。
- `relative` : 你可以使用`top`、`bottom` 、`left` 和 `right` 这些属性指定元素对于其页面中的自然位置的位移。
- `fixed` ：使元素不随滚动条移动而始终保持在相对浏览器视窗中的某个位置。用`top`、`bottom` 、`left` 和 `right` 设置。
- `absolute` ：设定元素对于其首个非 `static` 的父元素的相对位移。**注意：**要使用 `absolute` 属性，最好要把父元素设置为 `position: relative`。See [this post](https://css-tricks.com/absolute-positioning-inside-relative-positioning/) for more information.



In order to specify the location for a **`fixed`** element, use the `top`, `left`, `bottom`, and/or `right` properties to specify distance from the appropriate edge of the browser window:

```css
/* make the <nav> element fixed at the top of the browser window */
nav {
    position: fixed;
    top: 0;  /* 0px from the top */
    left: 0; /* 0px from the left */
    width: 100%; /* same as parent, useful for spanning the page */
}
```

### Floating

你也可以使用float来移动元素的位置。float的元素会安于屏幕的一隅，而其他内容会将其wrap起来。

如果你不想要其他内容wrap在float元素的周围，可以使用 `clear` 属性：

```css
.clear-float {
    clear: both; /* do not allow floating elements on either side */
}
```



**注意：**如果你只是想让某个元素安于页面一隅，你可以使用float。但更复杂的布局（比如多栏目结构）则需要更好的方法来实现。



### Flexbox

> Flexbox is a new standard that is now supported by [most modern browsers](http://caniuse.com/#feat=flexbox); it has a buggy implementation in Microsoft IE, but is supported in the standards-compliant Edge. For older browsers, you can instead rely on a grid system from one of the popular CSS Frameworks such as Bootstrap.



> Despite it’s capabilities, Flexbox still is designed primarily for one-directional flows (e.g., having one row of columns). To handle true grid-like layouts, browsers are adopting *another* emerging standard called [**Grid**](https://css-tricks.com/snippets/css/complete-guide-grid/). The Grid framework shares some conceptual similarities to Flexbox (configuring child elements inside of a parent container), but utilizes a different set of properties. Learning one should make it easy to pick up the other. Note that the grid framework is [less well supported](http://caniuse.com/#search=CSS grid) than even Flexbox (it is not supported by IE, Edge, or common older Android devices), so should be used with caution.



要使用flexbox，你需要一个容器元素来装载子元素：

```html
<div class="flex-container"> <!-- Parent -->
  <div class="flex-item">Child 1</div>
  <div class="flex-item">Child 2</div>
  <div class="flex-item">Child 3</div>
</div>
```

把容器元素设置为：

```css
.flex-container { /* my flexbox container class */
    display: flex;
}
```

flexbox默认是水平排列的，你可以修改容器元素的 `flex-direction` 属性来调整。见：[flex-direction](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#article-header-id-3)

除了 `height` 和 `width` 以外，flexbox有更加灵活的方式来设置这些属性：

所有flexbox容器的直接子元素都可以使用相关的属性来改变它们在flexbox容器中的排列方式，下面是三个主要的属性：

```css
* { box-sizing: border-box; } /* recommended for item sizing */

.flex-item {
    flex-grow: 1; /* get 1 share of extra space */
    flex-shrink: 0; /* do not shrink if items overflow container */
    flex-basis: 33%; /* take up 33% of parent initially */
}
```

- [**`flex-grow`**](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#article-header-id-10) 指定了额外的空间如何在子元素之间分配。

  > That is, if the container is `500px` wide, but the items’ only takes up `400px` of space, this property determines how much of the remaining `100px` is given to the item.

  > The value is a unitless number (e.g., `1` or `2`, defaulting to `0`), and the amount of remaining space is divided up *proportionally* among the items with a `flex-grow`. So an item with `flex-grow:2` will get twice as much of the remaining space as an item with `flex-grow:1`. If there are 4 items and `100px` of space remaining, giving each item `flex-grow:1` will cause each item to get `25px` (100/4) of the extra space. If one of the items has `flex-grow:2`, then it will get `40px` (21+1+1+2=25=40%21+1+1+2=25=40%) of the extra space, while the other three will only get `20px`.

  可以使用 `flex-grow:1` 让所有子元素在容器中均匀分布。.

- [**`flex-shrink`**](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#article-header-id-11) works similar to `flex-grow`, but in reverse. It takes as a value a number (default to `1`), which determine what “share” or ratio it should shrink by in order to accommodate any overflow space. If the specified dimensions of the items exceeds the dimensions of the container (e.g., 4 `100px` items in a `300px` container would have `100px` of “overflow”), the `flex-shrink` factor indicates how much size needs to be “taken off” the item. A higher number indicates a greater amount of shrinkage.

  In practice, you will often leave this property at default (by not specifying it), *except* when you want to make sure that an item does NOT shrink by giving it `flex-shrink:0`.

- [**`flex-basis`**](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#article-header-id-12) allows you to specify the “initial” dimensions of a particular item. This is similar in concept to the `width` property, except that `flex-basis` is more [flexible](https://stackoverflow.com/questions/34352140/what-are-the-differences-between-flex-basis-and-width) (e.g., if you change the `flex-direction` you don’t also have to change from `width` to `height`). Note that this value can be an dimensional measurement (absolute units like `100px`, or a relative unit like `25%`).



你也可以使用简写：`flex` 来指定上述3个属性：

> give the `flex-grow`, `flex-shrink`, and `flex-basis` values separated by spaces (the second two being optional if you want to use the default values). Note that if unspecified, the `flex-basis` property will be set to `0`, rather than the `auto` value it has if unspecified.



除此之外，容器中还可以使用这些元素来进行布局：

> The Flexbox framework also provides a number of additional properties that you can specify on the **container** to customize how items of different sizes are organized on the screen:
>
> - [`justify-content`](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#article-header-id-6) specifies how the items should be spread out across the container. Note that items that have `flex-grow:1` will use up the extra space, making this less relevant.
> - [`align-items`](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#article-header-id-7) is used to specify “cross-axis” alignment (e.g., the vertical alignment of items for a horizontal row).
> - [`flex-wrap`](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#article-header-id-4) is used to have items “wrap around” to the next line when they overflow the container *instead of* shrinking to fit. You can then use the [`align-content`](https://css-tricks.com/snippets/css/a-guide-to-flexbox/#article-header-id-8) property to specify how these “rows” should be aligned within the container.
>
> While it may seem like a lot of options, Flexbox layouts will allow you to easily create layouts (such as multi-column pages) that are otherwise very difficult with the regular box model. Moreover, these layouts will be flexible, and can easily be made **responsive** for different devices and screen sizes.



## 五、响应式设计

### 移动设备优先

为了进行更好的适配，我们优先适配屏幕受限最大的移动式设备。之后，我们可以通过“who comes last wins”的CSS规则对更大屏幕的设备进行适配，并在移动式设备的基础上增加更多效果。

原则：

- Layout：移动设备的页面布局应采用上下堆叠式布局，追求只需在垂直轴上下移动浏览。position属性为`fixed` 的元素应该尽可能少，以节省被占据的空间。
- Media：小尺寸屏幕需要的图片不需要太高的分辨率，同时，降低分辨率或者压缩媒体文件也可以节省加载时间。（大屏幕的设备带宽一般更足，所以可以使用高分辨率的媒体文件）
- Font：使用**相对字体单位**来适配移动设备。
- Navigation：太大的Navigation可能会因为wrapping到多行。建议使用小tab bar，或者菜单图标或者使其collapse来减少空间。
- 输入和互动：可点击和互动的元素要足够大。不要使用拥挤在一块的小图标和非常短的超链接。
- 内容：对于移动端和网页端用户，使用不同的方式表示同样的内容：比如在移动端显示一个巨大的电话图标，而在网页端直接显示电话号码。

### 指定Viewport

有的时候移动端浏览器会自动缩放网页内容来进行适配，但这有时候会导致内容太小，用户不得不再次手动放大。

说明viewport的size和scale可以避免这种问题。在html里面使用下列 `<meta>` 标签：

```html
<head>
  <meta charset="utf-8"> <!-- always need this -->
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

  <!-- more head elements, including <link> ... -->
</head>
```

一般我们指定3个属性：

- `width` ：指明设备的屏幕宽度（viewport）
- `initial-scale` ：初始页面的缩放值，一般为1，即不缩放
- `shrink-to-fit`：告诉浏览器不要缩放内容来进行适配

更多详见：[Viewport](https://developer.mozilla.org/en-US/docs/Glossary/Viewport)

### Media Queries

使用媒体查询来构建响应式设计。

媒体查询有点类似 `if` 语句。

```css
/* A normal CSS rule, will apply to all screen sizes */
body {
    font-size: 14px;
}

/* A Media Query */
@media (min-width: 768px)
{
    /* these rules apply ONLY on screens 768px and wider */

    /* a normal CSS rule */
    body {
        font-size: 18px;
        background-color: beige;
    }
    /* another CSS rule */
    .mobile-call-icon {
        display: none; /* don't show on large displays */
    }
}
```

格式就是 `@media ( expression ) { }` ，其中expression表示条件，`min-width: 768px` 表示viewport的width大于等于768px时生效，`max-width` 类似。

如果要使用与或非逻辑：

- `and` 和 `not` 表示与和非
- 用逗号隔开的规则表示或

```css
/* style rules for screens between 768px and 992px */
@media (min-width: 768px) and (max-width: 992px) { }

/* style rules for screens larger than 700px OR in landscape orientation */
@media (min-width: 700px), (orientation: landscape) { }
```



采用 Mobile First 的设计理念，我们把适配移动设备的规则放在最上方，然后在媒体查询里写针对大屏设备的规则来覆盖它们：

```css
/* on small mobile devices, the header has a purple background */
header {
    font-size: 1.2rem;
    background-color: mediumpurple;
}

/* on 768px OR LARGER displays */
@media (min-width: 768px) {
    header {
        font-size: 1.5rem; /* make the header larger font on larger displays */
    }
}

/* on 992px OR LARGER displays */
@media (min-width: 992px) {
    header {
        background-image: url('../img/banner.png') /* use background image */
    }
}
```

#### Flexbox案例

```html
<div class="row">
  <div class="column">column ONE content</div>
  <div class="column">column TWO content</div>
  <div class="column">column THREE content</div>
  <div class="column">column FOUR content</div>
</div>
```

```css
/* on devices 768px OR WIDER */
@media (min-width: 768px)
{
    .row { /* row is a flexbox container */
        display: flex;
        flex-wrap: wrap; /* wrap extra items to the next "line" */
    }

    .column  { /* column is a flexbox item */
        flex-basis: 50%; /* columns take up 50% of parent by default */
        flex-grow: 1;
    }
}

@media (min-width: 1200px) {
    .column {
        flex-basis: auto; /* columns are automatically sized based on content */
    }
}
```

CodePen演示：[Flexbox响应式设计](https://codepen.io/joelross/pen/YxvBzG)

## 六、CSS Framework

你可以使用现成的CSS框架来构建网页，而省去自己完全从0开始书写CSS的麻烦。

一些框架：

- [**Bootstrap**](http://getbootstrap.com/)
- [**Foundation**](http://foundation.zurb.com/)
- [**Material Components for the Web (MCW)**](https://material.io/components/web/)
- [**normalize.css**](http://necolas.github.io/normalize.css/)：If you choose **not** to use a framework, you should still include `normalize` to make your sites consistent across browsers!

### 使用框架

使用 `<link>` 标签来引入框架。

```html
<head>
  <!--... other elements here...-->

  <link rel="stylesheet"
        href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.3/dist/css/bootstrap.min.css">
  <link rel="stylesheet" href="css/my-style.css">
</head>
```

**注意：**请在你自己的css之前引入框架，这样你自己写的CSS才不会被覆盖。



你可以使用CDN来引入CSS：

> You can almost always switch between the minimized and non-minimized file just by changing the file name between `file.css` and `file.min.css` (when using a CDN).

或者，直接下载源代码到本地路径，然后通过相对路径引入。

你也可以使用**包管理器** ，比如`npm`来下载CSS框架：

```bash
# install bootstrap (latest version)
npm install bootstrap
```

> This will install the library’s source code into your project’s `node_modules/` folder. Because you’ve saved the dependency in your `package.json` file, you can use the `.gitignore` file to exclude the `node_modules/` folder from your code repo, having new developers install the required framework via `npm install`. You can also use `npm` to easily upgrade the packages (such as when a new version of Bootstrap comes out.

### Bootstrap

使用Bootstrap框架。

引入：

```html
<link rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.3/dist/css/bootstrap.min.css"
      integrity="sha384-rbsA2VBKQhggwzxH7pPCaAqO46MgnOM80zW1RWuH61DGLwZJEdK2Kadq2F9CUG65"
      crossorigin="anonymous">
```

> (This `<link>` example has 2 additional (optional) attributes: `integrity` gives a [cryptographic hash](https://en.wikipedia.org/wiki/Cryptographic_hash_function) of the source code so the browser can make sure you didn’t download a malicious file by mistake, and `crossorigin` which says that no credentials are sent to the remote server—see the [AJAX](https://info340.github.io/ajax.html#ajax) chapter for a more detailed discussion).

你可以使用特殊的class名来给html元素引入Bootstrap的CSS效果：

```html
<button>Plain button</button>
<button class="btn">Bootstrap button</button>
<button class="btn btn-primary">Primary colored button</button>
```

它也支持响应式设计：

| Screen Width | Abbreviation | Approximate Device Size                     |
| ------------ | ------------ | ------------------------------------------- |
| < 576px      | (default)    | Extra-small devices (e.g., portrait phones) |
| ≥ 576px      | `sm`         | Small devices (e.g., landscape phones)      |
| ≥ 768px      | `md`         | Medium devices (e.g., tablets)              |
| ≥ 992px      | `lg`         | Large devices (e.g., laptops)               |
| ≥ 1200px     | `xl`         | Extra-large devices (e.g., desktops)        |
| ≥ 1400px     | `xxl`        | Extra-large devices (e.g., big desktops)    |

使用方法：

> the `float-md-left` class will cause the element to float **only on medium \*or larger\* screens**. Similarly, the `float-lg-left` class would cause the element to float only on *large or larger* screens.

#### The Grid

在Bootstrap里使用Grid来进行布局，`col-#` 表示元素占据此行的某个比例。下例中，Item 1占 6/12，Item 2 占 4/12，剩下的 2/12 被分配给Item 3。

```html
<div class="row"> <!-- a row of items -->
    <div class="col-6">Item 1</div>
    <div class="col-4">Item 2</div>
    <div class="col">Item 3</div>
</div>
```

响应式设计：

```html
<div class="row">
    <div class="col-sm-6 col-md-3">Item 1</div>
    <div class="col-sm-6 col-md-3">Item 2</div>
    <div class="col-sm-6 col-md-3">Item 3</div>
    <div class="col-sm-6 col-md-3">Item 4</div>
</div>
```

(See a [CodePen](https://codepen.io/drstearns/pen/XaYBXp) of this code in action!)

#### 组件

Bootstrap还提供了很多组件，比如 `card` , `modal` , `navbar` 等等。

有一些组件需要用到 Javascript，所以你要引入Bootstrap的JavaScript库：

```html
<!-- the Bootstrap JavaScript library -->
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.2.3/dist/js/bootstrap.bundle.min.js"></script>
```

然后你可以使用  `data-` 属性 (e.g., `data-thingamabob` or `data-my-attribute`) 来指定JavaScript的作用目标。

```html
<!-- note that the `id` attribute is set to "exampleModal" -->
<div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h1 class="modal-title fs-5" id="exampleModalLabel">Modal title</h1>
        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
      </div>
      <div class="modal-body">
        ...
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
        <button type="button" class="btn btn-primary">Save changes</button>
      </div>
    </div>
  </div>
</div>

<!-- data-toggle must be set to "modal" and data-target must be set to
the value of the `id` attribute on the element that starts your modal markup -->
<button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal">
  Open the Modal
</button>
```

注意上面例子中的modal的id是`exampleModal`，初始的时候是隐藏的，而只有一个button显示。

这个button的 `data-bs-toggle="modal"`，且指定这个Modal为：`data-bs-target="#exampleModal"` 。

这样，点击button的时候就会唤出id为 `exampleModal` 的modal。

Try it out in [CodePen](https://codepen.io/drstearns/pen/dzKqNB).



**注意：**CSS框架和javascript框架可能不兼容！

> The JavaScript included in these CSS frameworks allows you to easily add interactive features, but **they do not mix well with more advanced JavaScript frameworks like React.** To work with React, you’d need to switch instead to a library such as [react-bootstrap](https://react-bootstrap.github.io/), which uses the Bootstrap CSS stylesheet, but re-implement the JavaScript portions to fit into the React framework.

