### 1. CSS 性能优化

- **如何通过 CSS 实现性能优化？**
  *使用高效的选择器（避免太深的层级嵌套），合并 CSS 文件减少 HTTP 请求，使用 CSS Variables 减少重复样式，避免使用不必要的动画。*
- **为什么 CSS 选择器的层级过深会影响性能？如何避免？**
  *过深的选择器层级会增加浏览器解析的工作量，导致渲染变慢。应尽量使用简单的选择器，如类选择器或 ID 选择器，避免不必要的嵌套。*
- **什么是“关键渲染路径”？如何通过 CSS 优化它？**
  *关键渲染路径是浏览器从获取 CSS 文件到页面渲染完成的过程。可以通过内联关键 CSS 和延迟加载非关键 CSS 文件来优化。*

### 2. 动画和过渡

- **`transform` 和 `opacity` 是如何帮助优化动画性能的？**
  *`transform` 和 `opacity` 不会触发页面重排（reflow）或重绘（repaint），只触发合成层更新，能大幅减少开销，提升动画流畅度。*

- **CSS 动画和 JavaScript 动画在性能上有什么区别？如何选择？**
  *CSS 动画性能更优，尤其在使用 `transform` 和 `opacity` 时；JavaScript 更适合复杂动画，控制力更强。*

- **使用 `@keyframes` 定义一个旋转动画，且要求旋转动画在 2 秒时减速停止。**

  ```css
  @keyframes spin {
      0% { transform: rotate(0deg); }
      100% { transform: rotate(360deg); }
  }
  .spin-slow {
      animation: spin 2s ease-out;
  }
  ```

### 3. 响应式设计与媒体查询

- **什么是响应式设计？有哪些常用的 CSS 技术来实现它？**
  *响应式设计根据不同设备调整布局，常用媒体查询（`@media`）、流式布局、百分比宽度和弹性图像来实现。*

- **如何实现一个移动端优先（Mobile-First）的设计？**
  *先编写适用于移动端的样式，然后在媒体查询中定义适用于更大屏幕的样式。*

- **`@media` 媒体查询如何根据设备的宽度、分辨率和方向进行调整？列举一些示例。**

  ```css
  @media (max-width: 768px) { /* 移动端样式 */ }
  @media (min-width: 1024px) { /* 桌面端样式 */ }
  @media (orientation: landscape) { /* 横屏样式 */ }
  ```

### 4. 兼容性和跨浏览器支持

- **如何确保 CSS 在不同浏览器上兼容？**
  *使用 CSS 前缀、规范化（normalize.css），并进行浏览器测试。*
- **什么是 CSS 后备（Fallback）样式？何时使用？**
  *为不支持特定功能的浏览器提供的替代样式，例如 `background: red;` 用于不支持渐变的浏览器。*
- **在开发中如何检测并解决特定浏览器中的样式问题？**
  *可以使用浏览器调试工具检查样式，或者通过条件注释、CSS Hacks 解决特定浏览器的样式问题。*

### 5. CSS 变量（CSS Custom Properties）

- **CSS 变量与 SASS 变量的主要区别是什么？CSS 变量的优点是什么？**
  *CSS 变量在运行时被解析，支持动态变化；而 SASS 变量在编译时静态解析。*

- **如何定义全局 CSS 变量？如何在不同的主题中使用 CSS 变量实现主题切换？**

  ```css
  :root {
      --primary-color: blue;
  }
  .theme-dark {
      --primary-color: black;
  }
  
  ```

- **CSS 变量能否在媒体查询中使用？如何使用？**
  *可以在媒体查询中重新定义 CSS 变量的值，适用于不同尺寸的响应式设计。*

### 6.什么是BFC，有何作用？

BFC，即块级格式上下文（Block Formatting Context），是 CSS 布局的一种机制，用于控制和管理块级元素的布局和定位。BFC 在页面布局中起到了重要的作用，可以解决一些布局问题并影响元素的外观和交互。

BFC 是 CSS 渲染中的一种独立布局环境，其内部元素的布局规则遵循特定的规则，且与外部元素隔离。简单来说，BFC 就像一个“隔离容器”，它内部的元素布局不会影响外部，反之亦然。

**BFC 的主要作用和特性包括**

1. `外边距折叠`： 在同一个 BFC 中，垂直方向的相邻块级元素的外边距会发生折叠（取较大值）。但注意: 如果两个元素不在同一个 BFC 中，外边距不会折叠。
2. `阻止margin重叠`: 当两个相邻块级子元素**分属于不同的BFC**时可以**阻止margin重叠**
3. `清除浮动`： BFC 可以通过浮动元素内部创建一个新的格式上下文，BFC的区域不会与float box重叠。
4. `定位与布局`： BFC 影响元素的定位和布局方式，使得元素在 BFC 内部遵循一些特定的规则，例如浮动元素不会覆盖 BFC 内部的非浮动元素。
5. `自适应宽度`： BFC 会自动计算浮动元素的宽度，从而使其适应容器宽度。
6. `环境隔离`： BFC 内部的元素和外部的元素相互隔离，不会受到外部环境的影响。

**如何创建 BFC**

1. 使用 float: left; 或 float: right; 创建浮动的块级元素。
2. 使用 position: absolute; 或 position: fixed; 创建绝对定位的元素。
3. 使用 display: inline-block; 创建行内块元素。
4. 使用 overflow: hidden;、overflow: auto; 或 display: flow-root; 在容器上创建 BFC。 

### 7. 深入理解 CSS 选择器

- **CSS 选择器的优先级规则是什么？**
  *优先级顺序为：`!important` > 内联样式 > ID 选择器 > 类选择器 / 属性选择器 / 伪类 > 元素选择器 / 伪元素 > 通配符选择器  > 继承样式 > 浏览器默认样式 。*
- **如何在 CSS 中实现伪类选择器的复合选择（如 `:nth-child`、`:not`）？**
  *可以组合使用 `:nth-child`、`:not` 等伪类，例如 `li:nth-child(2):not(.disabled)`。*
- **`:is()` 和 `:where()` 有什么区别？如何使用它们？**
  *`:is()` 和 `:where()` 都能简化复杂选择器，但 `:where()` 不增加优先级。

### 8. CSS 分层架构和组织方法

- **CSS 架构中 BEM、OOCSS、SMACSS 的区别是什么？如何选择？**
  *BEM 采用块-元素-修饰符的命名方式，用于清晰、模块化的样式管理。OOCSS (Object-Oriented CSS) 将样式拆分为结构和皮肤，重用性高。SMACSS (Scalable and Modular Architecture for CSS) 强调分层管理，适合大型项目。选择取决于项目规模、团队习惯和维护性需求。*
- **如何在项目中合理组织 CSS 文件结构？**
  *可以按模块、页面或功能划分文件夹（如 `components/`、`layout/`、`pages/`）。CSS 文件可以进一步分为变量、通用类、组件样式、页面样式等。*

### 9. 响应式单位

- **什么是 `vw` 和 `vh`，与传统单位有什么区别？**
  *`vw` 和 `vh` 分别代表视口宽度和高度的 1%，适用于根据视口大小动态变化的元素，而传统单位如 `px` 是固定的。*
  
  | 特性       | vw                 | vh                 | 百分比 (%)             |
  | ---------- | ------------------ | ------------------ | ---------------------- |
  | 参照对象   | 视口宽度           | 视口高度           | 父元素的尺寸           |
  | 动态性     | 随视口大小实时变化 | 随视口大小实时变化 | 随父元素尺寸变化       |
  | 适用范围   | 全局视口相关布局   | 全局视口相关布局   | 局部容器相关布局       |
  | 默认值影响 | 不依赖父元素       | 不依赖父元素       | 依赖父元素是否定义尺寸 |
  
- **何时使用 `rem` 而非 `em`？**
  *`rem` 是相对于根元素的大小，因此全局一致性更好，而 `em` 是相对于父元素大小，适用于局部控制字体大小的情况。*

### 10. 深入了解 CSS 的特殊属性

- **`content-visibility` 是什么？如何提升页面性能？**
  *`content-visibility: auto;` 可以延迟非可见内容的渲染，有助于加快初次加载速度。*
- **`aspect-ratio` 属性如何帮助响应式设计？**
  *可以通过设置 `aspect-ratio` 保持宽高比，从而自动缩放内容。*

### 11.什么是CSS的继承性，那些属性可继承，哪些不可以？

CSS 的继承性是指某些样式属性可以从父元素传递到其子元素，使得子元素继承父元素的样式属性。这意味着，如果父元素上设置了特定的样式，子元素可能会自动继承这些样式，而无需显式地在子元素上设置相同的样式。
**可继承的属性**

1. `文字相关属性`： font-family、font-size、font-weight、font-style 等。
2. `颜色属性`： color、background-color。
3. `文本相关属性`： line-height、text-align、text-transform 等。
4. `链接相关属性`： text-decoration、link、visited、hover、active 等。
5. `列表属性`： list-style-type、list-style-image 等。
6. `表格属性`： border-collapse、border-spacing 等。
7. `元素显示属性`： display、visibility。
8. `百分比属性`： 某些属性（如 padding、margin）中的百分比值可以继承。

**不可继承的属性**

1. `盒模型属性`： width、height、margin、padding、border 等。
2. `定位和布局属性`： position、top、right、bottom、left、float、clear 等。
3. `背景属性`： background-image、background-position、background-repeat 等。
4. `定位属性`： z-index。
5. `轮廓属性`： outline、outline-width、outline-style、outline-color。
6. `文字选择属性`： user-select、cursor。
7. `表格属性`： table-layout、border-collapse、border-spacing。
8. `元素显示属性`： display、visibility。 

### 14.什么是伪类，什么是伪元素，二者有何区别？

伪类（Pseudo-Class）和伪元素（Pseudo-Element）是用于选择 HTML 元素的特殊选择器，它们允许您为特定状态或位置的元素应用样式。虽然它们在名称上很相似，但它们具有不同的作用和用法。
**伪类（Pseudo-Class）**
伪类用于选择处于特定状态的元素，如用户鼠标悬停在元素上或链接处于不同状态（未访问、已访问、悬停、激活等）。`伪类以单冒号 : 开头，后面跟上伪类名称`。例如：

1. `:hover`：鼠标悬停在元素上时应用样式。
2. `:active`：元素被激活（例如鼠标点击）时应用样式。
3. `:visited`：链接已被访问时应用样式。
4. `:nth-child(n)`：选择某个父元素的第 n 个子元素。

```css
a:hover {
  color: red;
}

li:nth-child(odd) {
  background-color: lightgray;
}
```

**伪元素（Pseudo-Element）**
伪元素用于在元素的特定位置插入内容或样式，如在元素的前面或后面插入额外的内容。`伪元素以双冒号 :: 开头，后面跟上伪元素名称`。例如：

1. `::before`：在元素的前面插入内容或样式。
2. `::after`：在元素的后面插入内容或样式。
3. `::first-line`：选择元素的第一行文字。
4. `::first-letter`：选择元素的第一个字母。

```css
p::before {
  content: "Before content: ";
}

p::first-letter {
  font-size: larger;
  font-weight: bold;
}
```

### 15.什么是盒子模型？

盒子模型（Box Model）是用于描述 HTML 元素在页面中占据空间的概念。每个 HTML 元素都被视为一个矩形的盒子，这个盒子由四个区域组成：内容区域、内边距（Padding）、边框（Border）和外边距（Margin）。这些区域组合在一起形成了元素的视觉和空间特性。
**盒子模型的组成部分**

1. `内容区域（Content）`： 这是元素内部实际容纳内容的区域，例如文本、图像等。
2. `内边距（Padding）`： 内边距是位于内容区域和边框之间的空白区域。内边距可以用来控制内容与边框之间的间隔。
3. `边框（Border）`： 边框是包围内容和内边距的线条，用于给元素添加边界效果。
4. `外边距（Margin）`： 外边距是位于元素边框以外的区域，用于控制元素与其他元素之间的间距。

```*
+---------------------------+
|        Margin            |
|  +---------------------+  |
|  |      Border        |  |
|  |  +-------------+   |  |
|  |  |  Padding   |   |  |
|  |  | +---------+ |   |  |
|  |  | | Content | |   |  |
|  |  | +---------+ |   |  |
|  |  +-------------+   |  |
|  +---------------------+  |
+---------------------------+
```

**盒子模型的两种类型：**

```css
  box-sizing: border-box / content-Box ;
```

标准盒子模型 (Content-Box)

```*
总宽度 = width + padding-left + padding-right + border-left + border-right
总高度 = height + padding-top + padding-bottom + border-top + border-bottom
```

替代盒子模型 (Border-Box)

```*
总宽度 = width（内容宽度会自动缩小以适应内边距和边框）
总高度 = height（内容高度会自动缩小以适应内边距和边框）
```

### 16.什么是浮动布局，如何清除浮动？

浮动布局（Float Layout）是一种常用的网页布局技术，通过将元素浮动在其父元素内，使得其他元素能够环绕浮动元素。浮动布局在过去被广泛用于创建多列布局、图文混排等效果。然而，随着 CSS Flexbox 和 CSS Grid 的出现，浮动布局逐渐被这些新的布局技术所取代。要创建浮动布局，可以使用 CSS 中的 float 属性。以下是一个简单的示例：

```html
<div class="container">
    <div class="left-column">Left Column</div>
    <div class="right-column">Right Column</div>
</div>
```

```css
.left-column {
    float: left;
    width: 50%;
}

.right-column {
    float: left;
    width: 50%;
}
```

上面的示例中，左列和右列会浮动在其容器内，实现了两列的布局。然而，使用浮动布局可能会引发一些问题，例如容器高度塌陷、布局错位等。因此，在使用浮动布局时，需要注意浮动元素的影响。
**清除浮动（Clearing Floats）**
浮动元素可能导致其父容器高度不准确或塌陷，为了解决这些问题，通常需要进行浮动清除。以下是一些常见的清除浮动的方法：

1. `使用空的块级元素清除浮动`： 在浮动元素后添加一个空的块级元素，并为其应用 clear: both; 样式。这会在浮动元素之后创建一个新的块级元素，从而使父容器正确包裹浮动元素。
2. `使用伪元素清除浮动`： 可以通过在父容器上使用伪元素来清除浮动。
3. `使用 overflow: hidden; 清除浮动`： 将父容器的 overflow 属性设置为 hidden 会使其包裹浮动元素。
4. `使用 display: flow-root; 清除浮动`： display: flow-root; 可以在父容器内创建一个 BFC（块级格式上下文），从而清除浮动。

### 17.CSS中有哪些定位，分别有什么作用和区别？

在 CSS 中，有几种不同的定位方式，用于控制元素在页面中的位置和布局。这些定位方式包括`相对定位（Relative Positioning）`、`绝对定位（Absolute Positioning）`、`固定定位（Fixed Positioning）`和`粘性定位（Sticky Positioning）`。

下面是它们的作用和区别：
**相对定位（Relative Positioning）**

1. 使用 position: relative; 可以将元素相对于其正常位置进行定位，不会影响其他元素的布局。
2. 相对定位允许使用 top、right、bottom 和 left 属性来移动元素相对于其原始位置的距离。
3. 相对定位的元素仍然占据其原始空间，周围的元素不会受到影响。

**绝对定位（Absolute Positioning）**

1. 使用 position: absolute; 可以将元素相对于其最近的已定位的父元素进行定位，如果没有已定位的父元素，则相对于文档的初始坐标。
2. 绝对定位的元素不会在正常文档流中占据空间，而是浮动在其他元素之上。
3. 绝对定位允许使用 top、right、bottom 和 left 属性来指定元素的位置。

**固定定位（Fixed Positioning）**

1. 使用 position: fixed; 可以将元素固定在视窗的某个位置，即使页面滚动，元素也会保持在指定的位置。
2. 固定定位通常用于创建固定的导航栏、页脚或其他在页面滚动时始终可见的元素。

**粘性定位（Sticky Positioning）**

1. 使用 position: sticky; 可以将元素在滚动过程中根据其父容器的可视区域进行定位，当元素到达指定位置时，会"粘"在页面上。
2. 粘性定位在元素未滚动到特定位置前会保持相对定位，一旦达到特定位置，就会变为固定定位。

### 18.隐藏元素有哪些方法，有何区别？

`display: none`

1. 这是最常见的隐藏元素的方法。
2. 元素不会在页面上显示，也不会占据空间。
3. 元素不会渲染，对布局没有影响。

`visibility: hidden`

1. 元素在页面上不可见，但仍然占据空间。
2. 元素会渲染，但不会显示在页面上，对布局会产生影响。

`opacity: 0`

1. 元素透明度被设置为 0，即完全透明。
2. 元素仍然占据空间，但在视觉上不可见。

`position: absolute; left: -9999px`

1. 将元素定位到页面外部，远离可视区域。
2. 元素不会在页面上显示，但仍然占据空间。

`Clip-path 或 Mask`

1. 使用 CSS 属性 clip-path 或 mask 将元素的内容裁剪掉。
2. 元素的内容在视觉上不可见，但仍然占据空间。

`Visibility: collapse;（用于表格元素）`

1. 用于表格元素的特定隐藏方法。
2. 表格行被隐藏，但仍然占据空间，与 display: none; 不同，表格布局不会被破坏。

### 19.给元素设置负外边距会发生什么？

给元素设置负外边距（negative margin）会导致一些特定的布局效果和影响，可能会出现一些意想不到的结果。负外边距会影响元素的位置和与其他元素的关系，具体效果取决于元素的类型、位置和周围元素的布局。以下是一些可能发生的情况和影响：

1. `重叠与间距`： 负外边距可能导致元素与其他元素重叠，或者在元素之间创建更大的间距。
2. `元素移动`： 负外边距可以将元素移动到其正常位置之外。例如，设置负的 margin-top 可以使元素向上移动。
3. `边界框重叠`： 负外边距可能导致元素的边界框（margin box）与其他元素的边界框发生重叠，可能影响布局。
4. `父元素高度塌陷`： 如果给一个元素设置负的 margin-top，并且父元素没有进行清除浮动或包含浮动元素，可能导致父元素的高度塌陷。
5. `相邻元素的影响`： 负外边距可能影响相邻元素的布局，导致它们的位置改变。
6. `定位与堆叠上下文`： 负外边距可能影响元素的定位，尤其是在涉及堆叠上下文的情况下。

### 20.什么是外边距重叠，会有什么结果？

外边距重叠（Margin Collapsing）是指在特定条件下，相邻的两个或多个元素的外边距会合并成一个较大的外边距，从而导致元素之间的间距比预期的要大。外边距重叠通常会在垂直方向上发生，并且会影响元素的垂直间距和布局。
**外边距重叠会在以下情况下发生**

1. `相邻元素`： 当两个相邻的块级元素之间没有边框、内边距和内联内容（即没有隔断的容器）时，它们的上下外边距可能会发生重叠。
2. `父子元素`： 如果父元素的外边距与其第一个或最后一个子元素的外边距重叠，也会发生外边距重叠。
3. `空元素`： 如果一个没有实际内容的块级元素的外边距与其兄弟元素的外边距相遇，也可能发生外边距重叠。

 **外边距重叠可能会导致以下结果**

1. 间距变大： 外边距重叠会导致相邻元素之间的间距比预期的要大，这可能不符合设计意图。
2. 布局问题： 外边距重叠可能影响页面布局，导致元素的位置发生变化。
3. 塌陷问题： 外边距重叠可能导致父元素的高度塌陷，使其高度变得不准确。

**为了避免外边距重叠，可以采取以下措施**

1. 给元素添加边框、内边距、背景色等，这可以阻止外边距重叠。
2. 使用空的 border、padding 或 inline-block 布局技巧来破坏外边距重叠。
3. 使用 overflow: hidden; 或 display: flow-root; 在父元素上创建 BFC（块级格式上下文），从而阻止外边距重叠。

### 21.什么是物理像素，逻辑像素和像素密度？

物理像素（Physical Pixel）

1. 物理像素是显示器或屏幕上的最小可见单位，通常由一个发光的点或一个子像素组成。
2. 物理像素决定了屏幕的分辨率，即屏幕上可以显示多少个物理像素。
3. 不同设备的物理像素可能大小不同，例如高分辨率屏幕的物理像素可能更小。

逻辑像素（CSS Pixel / Device-Independent Pixel）

1. 逻辑像素是在 CSS 和 Web 开发中使用的虚拟单位，用于定义元素的尺寸和布局。
2. 逻辑像素是抽象概念，不与实际的物理像素一一对应，而是被映射到物理像素上。
3. 在标准情况下，一个逻辑像素会映射到一个物理像素，但在高分辨率设备上，可能会映射到多个物理像素，从而提供更高的清晰度。

像素密度（Pixel Density）

1. 像素密度是指在给定区域内物理像素的数量。通常以“每英寸像素数”（PPI）表示。
2. 高像素密度意味着在同样的物理尺寸内有更多的像素，从而提供更高的分辨率和图像质量。
3. 一般来说，高分辨率设备拥有更高的像素密度，例如高清电视、智能手机、平板电脑等。

逻辑像素和像素密度之间的关系：`在高分辨率设备上，一个逻辑像素可能映射到多个物理像素，这就是所谓的“像素密度倍增”`。这意味着开发人员可以使用相同的逻辑像素单位来处理不同像素密度的设备，从而实现统一的界面和用户体验。

### 22.z-index属性在什么情况下会失效？

z-index 属性用于控制元素在堆叠上下文中的显示顺序，即元素的层叠顺序。然而，有一些情况下 z-index 属性可能会失效或产生意外的结果：

1. `未创建层叠上下文`： z-index 只在层叠上下文中有效。如果某个元素未创建层叠上下文（例如，通过设置 position、opacity、transform 等属性），那么 z-index 可能不会按预期工作。
2. `父元素没有设置定位`： 如果父元素未设置定位属性（例如，position: relative; 或 position: absolute;），子元素的 z-index 可能无法正确生效。
3. `相对定位的元素`： 如果一个元素使用相对定位（position: relative;），而且没有设置 z-index，那么该元素的层叠顺序可能会被其他具有 z-index 值的元素覆盖。
4. `父子关系`： 如果父元素和子元素都设置了 z-index，则子元素的 z-index 不会影响父元素之间的层叠关系。子元素的层叠顺序仅影响同一父元素下的其他子元素。
5. `Flexbox 布局`： 在某些情况下，Flexbox 容器的 z-index 行为可能会比预期复杂。使用 z-index 来影响 Flexbox 内部的元素堆叠顺序时要特别注意。
6. `浮动元素`： 浮动元素可能会与定位元素的 z-index 发生冲突，导致层叠关系出现问题。

**为避免 z-index 失效的问题，您可以考虑以下几点**

1. 确保定位元素的父元素设置了定位属性，以创建正确的层叠上下文。
2. 避免过度使用 z-index，尽量采用更简单的布局方案。
3. 使用开发者工具检查元素的层叠顺序，以确定是否达到了预期效果。
4. 将元素的 z-index 值设置为大于或小于其他元素的值，而不是随意的值，以确保正确的层叠顺序。

### 23.如何在CSS方面做性能优化？

在CSS方面进行性能优化是确保网页加载速度和用户体验的重要部分。以下是一些在CSS中实施性能优化的方法：

1. `减少文件大小`： 使用压缩和合并工具，将多个CSS文件合并为一个，减少HTTP请求次数，从而减少文件大小。可以使用工具如Webpack、Gulp、Grunt等来自动化这个过程。
2. `压缩CSS`： 使用CSS压缩工具，例如CSS Minifier，来移除不必要的空格、注释和换行符，减小文件体积。
3. `精简选择器`： 尽量避免使用过于复杂的选择器，这样可以减少匹配的计算量，提高选择器的效率。
4. `避免使用@import`： 避免使用`@import`指令来加载外部样式，因为它会增加页面加载时间。而是使用`<link>`标签来加载外部样式表。
5. `使用CSS Sprites`： 将多个小图标或背景图片合并到一个雪碧图中，以减少HTTP请求次数。
6. `使用缓存`： 设置适当的缓存头，让浏览器缓存CSS文件，以减少重复加载。
7. `使用字体图标`： 使用字体图标（如Font Awesome）来替代图片图标，减少图片加载。
8. `避免使用!important`： 避免滥用!important，它可能导致难以管理的样式。
9. `使用内联样式`： 将重要的关键样式内联到HTML文件中，避免页面在加载时出现没有样式的闪烁。
10. `利用浏览器缓存`： 使用长期有效的URL，利用浏览器缓存，使用户下次访问时不需要重新下载CSS文件。
11. `按需加载`： 根据页面需要，只加载必要的CSS，避免加载不需要的样式。
12. `优化动画`： 对于使用CSS动画的元素，使用硬件加速（如transform、opacity）来提高性能。
13. `使用媒体查询`： 使用媒体查询创建响应式设计，避免加载不必要的样式。
14. `减少层叠深度`： 减少不必要的嵌套，减少层叠深度，提高渲染性能。
15. `尽量避免使用全局样式`： 使用模块化和组件化的CSS，减少全局样式的使用。 

### 24.什么是重绘，重排，如何避免？

重排（Reflow）触发:

- 改变元素的尺寸（width、height、padding、margin 等）。
- 改变元素位置（top、left、position 等）。
- 添加或删除 DOM 元素。
- 浏览器窗口大小变化（resize 事件）。
- 获取某些属性（如 offsetWidth、clientHeight），触发强制重排。

重绘（Repaint）触发:

- 改变颜色（color、background-color）。
- 改变可见性（visibility、opacity）。
- 重排必然触发重绘，但重绘不一定触发重排。

 **CSS 优化方法**

(1) 使用 GPU 加速（避免重排）

- 方法: 使用 transform 和 opacity 替代传统的布局属性（如 top、left、width）。

- 原因: transform 和 opacity 仅影响合成层（Composite），不会触发重排或重绘。

- 示例:

  ```css
  /* 避免重排 */
  .move {
    transform: translateX(100px); /* 推荐 */
  }
  /* 会触发重排 */
  .move {
    left: 100px; /* 不推荐 */
  }
  ```

(2) 减少选择器复杂度

- 方法: 避免过于复杂的选择器（如 div p span）。

- 原因: 复杂选择器需要浏览器匹配更多元素，可能触发大范围重绘。

- 示例:

  ```css
  /* 推荐 */
  .item {
    color: red;
  }
  /* 不推荐 */
  body div ul li .item {
    color: red;
  }
  ```

(3) 使用 will-change

- 方法: 提前告诉浏览器某个元素会发生变化，让其单独优化。

- 示例:

  ```css
  .animated {
    will-change: transform, opacity;
  }
  ```

- 注意: 滥用会导致内存占用增加，仅用于频繁变化的元素。

(4) 控制可见性

- 方法: 用 visibility: hidden 替代 display: none。

- 原因: display: none 会触发重排（移除元素），而 visibility: hidden 只触发重绘。

- 示例:

  ```css
  .hide {
    visibility: hidden; /* 推荐 */
  }
  ```

(5) 避免逐帧修改样式

- 方法: 使用 CSS 动画或 transition 替代 JavaScript 逐帧调整。

- 原因: CSS 动画由浏览器优化，利用 GPU，减少重排。

- 示例:

  ```css
  .fade {
    transition: opacity 0.3s ease;
  }
  .fade:hover {
    opacity: 0.5;
  }
  ```

3. JavaScript 优化方法

(1) 批量操作 DOM

- 方法: 将多次 DOM 修改合并为一次操作。

- 原因: 每次 DOM 修改（如添加元素、更改样式）都可能触发重排。

- 示例:

  ```javascript
  // 不推荐：多次修改触发多次重排
  const div = document.querySelector('div');
  div.style.width = '100px';
  div.style.height = '100px';
  div.style.background = 'red';
  
  // 推荐：批量修改
  div.style.cssText = 'width: 100px; height: 100px; background: red;';
  ```

(2) 使用 DocumentFragment

- 方法: 在内存中操作 DOM，完成后一次性添加到页面。

- 原因: 减少直接操作 DOM 树的重排。

- 示例:

  ```javascript
  const fragment = document.createDocumentFragment();
  for (let i = 0; i < 100; i++) {
    const div = document.createElement('div');
    div.textContent = `Item ${i}`;
    fragment.appendChild(div);
  }
  document.body.appendChild(fragment); // 一次性添加
  ```

(3) 避免频繁读取布局属性

- 方法: 缓存布局属性（如 offsetWidth），避免在修改样式后立即读取。

- 原因: 读取布局属性会强制浏览器完成挂起的重排（强制同步布局）。

- 示例:

  ```javascript
  // 不推荐：修改后立即读取
  element.style.width = '100px';
  console.log(element.offsetWidth); // 触发重排
  
  // 推荐：缓存或延迟读取
  const width = element.offsetWidth;
  element.style.width = '100px';
  ```

(4) 防抖和节流

- 方法: 在 resize 或 scroll 事件中使用防抖（debounce）或节流（throttle）。

- 原因: 频繁触发事件会导致多次重排。

- 示例:

  ```javascript
  function debounce(fn, delay) {
    let timer;
    return function () {
      clearTimeout(timer);
      timer = setTimeout(fn, delay);
    };
  }
  
  window.addEventListener('resize', debounce(() => {
    document.body.style.width = window.innerWidth + 'px';
  }, 200));
  ```

(5) 使用 requestAnimationFrame

- 方法: 将样式修改放入 requestAnimationFrame 中。

- 原因: 浏览器会在下一帧统一处理，减少重排次数。

- 示例:

  ```javascript
  requestAnimationFrame(() => {
    element.style.transform = 'translateX(100px)';
  });
  ```

4. 其他优化策略

(1) 减少 DOM 深度

- 方法: 简化 HTML 结构，减少嵌套层级。
- 原因: DOM 树越深，重排计算成本越高。

(2) 使用固定定位

- 方法: 对动画元素使用 position: absolute 或 fixed。

- 原因: 脱离文档流，变化时不会影响其他元素。

- 示例:

  ```css
  .fixed {
    position: absolute;
    top: 0;
    left: 0;
  }
  ```

(3) 隐藏不必要的元素

- 方法: 在操作大量元素前，将其从布局中移除，完成后还原。

- 示例:

  ```javascript
  element.style.display = 'none';
  // 执行大量 DOM 操作
  element.style.display = 'block';
  ```

(4) 限制重排范围

- 方法: 使用 BFC 或 Flexbox 隔离布局。

- 原因: BFC 内的重排不会影响外部。

- 示例:

  ```css
  .container {
    overflow: hidden; /* 触发 BFC */
  }
  ```

总结

- CSS: 用 transform 和 opacity 替代布局属性，使用 transition 和 will-change。
- JavaScript: 批量操作 DOM、使用 DocumentFragment、缓存布局属性、防抖节流。
- 其他: 简化结构、使用固定定位、限制重排范围。
- 核心原则: 减少触发次数、缩小影响范围、利用浏览器优化（如 GPU 加速）。

如果有具体的重绘/重排问题（比如某个动画卡顿），可以告诉我，我再帮你分析优化方案！

### 26.px，em，rem有何区别，各自适用场景是什么？

px、em 和 rem 都是用来定义字体大小和元素尺寸的单位，但它们在应用和计算方式上有一些不同。以下是它们的区别和适用场景：
`px（像素）`

1. px 是绝对单位，表示一个设备上的物理像素。
2. 字体大小和元素尺寸使用 px 单位会在各种设备上保持一致的精确度，但不适应不同屏幕尺寸和分辨率的变化。
3. 适用场景：当需要确保字体和元素尺寸在不同设备上保持一致时，例如设计稿上的精确尺寸。

`em`

1. em 是相对单位，基于元素的字体大小。一个 em 单位等于父元素的字体大小。
2. 如果某个元素的字体大小为 16px，那么 1em 就等于 16px。
3. em 可以被继承，并且可以在元素嵌套时进行累积计算，但可能会导致样式的复杂性。
4. 适用场景：用于相对于父元素字体大小进行调整，或在复杂的嵌套结构中调整字体和尺寸。

`rem（根元素倍数）`

1. rem 是相对单位，基于根元素的字体大小。根元素通常是  元素。
2. 如果根元素的字体大小为 16px，那么 1rem 就等于 16px。
3. rem 具有继承的特性，但不会受到父元素字体大小的影响，只受根元素字体大小影响。
4. rem 更适合用于响应式设计，因为只需在根元素上调整字体大小即可影响整个页面。
5. 适用场景：用于实现响应式布局和尺寸调整，特别是在移动设备和桌面设备上。

### 27. CSS3的多列布局（Multi-Column Layout）有哪些属性，如何使用？

CSS3的多列布局（Multi-Column Layout）允许将内容分割成多个列，以实现更多的内容呈现和布局控制。以下是多列布局的属性以及如何使用它们：
**多列布局属性**

1. `column-count`：指定要创建的列数。
2. `column-width`：指定每列的最小宽度。
3. `column-gap`：指定列之间的间隔。
4. `column-rule`：设置列之间的边框样式、宽度和颜色。
5. `column-rule-width、column-rule-style、column-rule-color`：分别设置列之间边框的宽度、样式和颜色。
6. `column-span`：指定元素跨越的列数。

```css
/* 基本语法 */
.element {
  column-count: count;
  column-width: width;
  column-gap: gap;
  column-rule: width style color;
  column-rule-width: width;
  column-rule-style: style;
  column-rule-color: color;
  column-span: span;
}

/* 例子：创建多列布局 */
.content {
  column-count: 2;
  column-width: 200px;
  column-gap: 20px;
}

/* 例子：为列添加边框 */
.content {
  column-count: 3;
  column-width: 150px;
  column-gap: 10px;
  column-rule: 1px solid #ccc;
}
```

### 28.CSS3的弹性布局（Flexbox）有哪些属性，如何使用？

CSS3 弹性布局（Flexbox）是一种用于进行灵活的、自适应的布局的模型，通过一些属性来定义容器和其内部项目的排列方式。以下是弹性布局的属性以及如何使用它们：
**容器属性（适用于弹性容器）**

1. `display`：设置容器的显示类型为 flex。
2. `flex-direction`：设置主轴的方向，可以是 row（默认）、row-reverse、column、column-reverse。
3. `flex-wrap`：设置项目是否换行，可以是 nowrap（默认）、wrap、wrap-reverse。
4. `flex-flow`：是 flex-direction 和 flex-wrap 的缩写。
5. `justify-content`：设置主轴上项目的对齐方式，可以是 flex-start、flex-end、center、space-between、space-around。
6. `align-items`：设置交叉轴上项目的对齐方式，可以是 flex-start、flex-end、center、baseline、stretch。
7. `align-content`：在多行情况下，设置多行的对齐方式，可以是 flex-start、flex-end、center、space-between、space-around、stretch。

**项目属性（适用于弹性项目）**

1. `order`：设置项目的显示顺序，数值越小越靠前。
2. `flex-grow`：设置项目的放大比例，用于分配多余空间。
3. `flex-shrink`：设置项目的缩小比例，用于收缩空间。
4. `flex-basis`：设置项目的初始大小，可以是长度值或 auto。
5. `flex`：是 flex-grow、flex-shrink、flex-basis 的缩写。
6. `align-self`：设置单个项目的交叉轴对齐方式，可以覆盖 align-items。

```css
/* 基本语法 */
.container {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  justify-content: flex-start;
  align-items: stretch;
}

/* 例子：水平居中和垂直居中 */
.container {
  display: flex;
  justify-content: center;
  align-items: center;
}

/* 例子：左对齐和上对齐 */
.container {
  display: flex;
  justify-content: flex-start;
  align-items: flex-start;
}
```

### 29. CSS3的媒体特性（Media Features）有哪些，如何使用？

CSS3 的媒体特性（Media Features）允许您根据设备的属性和特性应用不同的样式，从而实现响应式设计。以下是一些常见的媒体特性以及如何使用它们：
**宽度和高度**
`width`：视口的宽度。
`height`：视口的高度。
**设备方向**
`orientation`：设备的方向，可以是 portrait（纵向）或 landscape（横向）。
**屏幕分辨率**
`resolution`：屏幕分辨率。
**视口特性**
`aspect-ratio`：视口的宽高比。
`device-aspect-ratio`：设备的宽高比。
**像素密度**
`min-device-pixel-ratio、max-device-pixel-ratio`：最小和最大设备像素比。
`min-resolution、max-resolution`：最小和最大分辨率。
**颜色**
`color`：设备支持的颜色位数。
`color-index`：设备支持的调色板索引数。
**触摸和指针**
`hover`：设备是否支持鼠标指针悬停。
`pointer`：设备是否支持指针设备。

```css
/* 基本语法 */
@media media-feature {
  /* 样式规则 */
}

/* 例子：根据屏幕宽度应用不同的样式 */
@media (max-width: 768px) {
  body {
    font-size: 14px;
  }
}

/* 例子：根据设备方向应用不同的样式 */
@media (orientation: landscape) {
  /* 横向布局的样式 */
}

/* 例子：根据设备像素比应用不同的样式 */
@media (min-device-pixel-ratio: 2) {
  /* 高像素密度的样式 */
}

```

在上述示例中，@media 媒体查询用于根据不同的媒体特性应用不同的样式。您可以根据不同的媒体特性来定义样式规则，从而实现响应式设计，以适应不同的设备和屏幕。

### 30.什么是媒体查询，有什么作用？

媒体查询（Media Queries）是一种在 CSS 中用于针对不同设备和屏幕尺寸应用不同样式的技术。通过媒体查询，开发人员可以根据设备的特性（如屏幕宽度、高度、方向、像素密度等）来应用不同的样式规则，从而实现响应式设计和适配不同的屏幕和设备。媒体查询的作用包括：

1. `响应式设计`： 媒体查询使得网页能够根据不同设备的特性和屏幕尺寸自动调整布局和样式，以适应不同的屏幕大小和分辨率。
2. `移动优先设计`： 使用媒体查询，开发人员可以首先为移动设备设计样式，然后在更大的屏幕上逐渐增加适当的样式，实现移动优先的设计理念。
3. `适配不同设备`： 媒体查询允许开发人员针对不同设备类型和特性（如手机、平板、电脑、打印机等）创建专门的样式，从而提供更好的用户体验。
4. `减少加载时间`： 通过在适用设备上应用特定样式，可以减少不必要的样式和资源的加载，提高网页加载性能。
5. `单一代码库`： 使用媒体查询，可以在一个 CSS 文件中管理不同设备的样式，从而避免维护多个样式文件。

```css
/* 默认样式 */
body {
  background-color: white;
  color: black;
}

/* 在窗口宽度小于 600px 时应用的样式 */
@media (max-width: 600px) {
  body {
    background-color: lightgray;
    color: darkgray;
  }
}
```
