本章主要讨论 CSS 3 中加入的`flexbox`布局。

新的`display:flex`布局模式是作为 CSS 2.1 中的布局的替代品出现的。它在现代浏览器中有着广泛的支持，这意味着它可以在 IE 10 以上的版本中使用。

CSS 布局一个一贯存在的问题是，控制负空间(实际内容之间的空间)的分配方式有困难。在许多情况下，你无法强制用户使用特定的视口/显示大小，也无法完全控制需要在Web应用程序的特定部分中显示多少子项。

CSS 3的`flexbox`布局是解决管理负空间的一个方案。它提供了很多一个`flexbox`的父元素中子元素如何控制大小，包装和对齐。

`flexbox`的基本假设是，作为控制者，你希望`flexbox`的中的内容能在下面两种情况中进行很好的控制：

- 当父级元素可用宽度和高度有变化的情况
- 容器中子项数有变化的情况

`flex`在`flexbox`中指的是当可用宽度或高度大于或小于子框所需的'理想'量时，指定如何调整子项大小的能力。

## 1 Flexbox Properties

`flexbox`属性可以分为两部分：设置在`父元素`上的属性和设置在`子元素`上的属性。

- `flex容器`的属性
  - `display:flex`和`display:inline-flex`
  - `flex-flow`(缩写)
    - `flex-direction`：子元素堆叠的方向
    - `flex-warp`：flex项能否包裹其他flex行
  - `justify-content`：元素在主轴(main axis)上如何定位
  - `align-items`：元素在副轴(cross axis)如何相对它们的 flex lines 定位
  - `align-content`：flex lines 是如何相对副轴上的父元素定位

从这些属性上可以看出，`flexbox`的父元素在某种程度上类似常规段落的文本(例如内联级格式化上下文)，但不像正常的段落或者一个列表行，`flexbox`的父元素可以决定子元素的堆叠方向为水平(`flex-direction:row`)或者垂直(`flex-direction:column`)。

- `flex项目`的属性
  - `order`：元素的顺序
  - `flex`(缩写)
    - `flex-grow`：当主轴上留有空间时，分配给子元素的空间比例
    - `flex-shrink`：当主轴上没有足够的空间时，分配给子元素的负空间(收缩)的比例
    - `flex-basis`：子项的内容维度如何影响`flex-grow`和`flex-shrink`的计算
  - `align-self`：子元素用来复写父元素`align-items`属性

如果只有一个属性值得注意的话，那就是`flex-basis`，它是决定 flex resizing 和 flex 项目如何放置在 flex lines 上的关键。