本章，我们会介绍不同的 CSS 布局属性是怎么影响大小，位置以及页面的所有布局。同时会提供一些谜题的讨论帮助你复习在之前几章学习到的内容。

本章是按照使用案例而不是属性或者特性来组织。首先，我们讨论大小和位置，然后会聚焦在两个特殊属性的使用上：grid-base 的布局，垂直和水平居中。

### CSS layout tricks and techniques used for sizing

与大小相关的技术允许您定义特定元素的大小，随着视口大小的变化，它应该如何增长以及它应该如何缩小。

- Height transfer
- Forced min-height
- Combining flex and non-flex items
- Sizing with constraints

`Height transfer`和基本的 HTML 文档不同，网络应用经常希望能占据视口的所有可用的空间以避免滚动条的出现。然而，典型的 HTML 元素包含几个有着默认属性`display:block`的根元素。如果这些盒没有特定的高度，它们将使用整个容器的高度而不是视口的高度。下面的代码强制`html`和`body`标签设置为视口高度的`100%`，这可以让你在后续标记中使用百分比引用视口高度。

```html
html, body {
  height: 100%;
}
```

CSS 3 增加了`vh`和`vw`单位，它始终指向视口的宽度和高度，这使得将元素的大小和视口关联更简单了，你不需要让树中的每个父项都传输视口的高度。

`min-height:100%`在 CSS 2.1中将元素对齐在父盒子的底部是很难的，因为盒子不是水平的从左至右的叠在一起，就是竖直的从上到下的叠在一起。设置`min-height`可以保证 div 是正常定位在和页面底部平齐的位置。

`结合 flex-grow:0 和 flex-grow:1`，flexbox 提供了强力的工具来控制元素的大小。Philip Walton's [Solved by Flexbox](http://philipwalton.github.io/solved-by-flexbox/)覆盖了几个额外的布局，但是这里我要指出一个特殊的技巧，使用`flex-grow:0`和`flex-grow:1`生成一个底部或顶部对齐的盒子，就像一个 header 或者 footer。原因非常简单：将主要内容放置在`flex-grow:1` flex 项中，并将 header 或 footer 放在`flex-grow:0`的 flex 项中。假设 flex 父级的大小为其父级的百分比，主内容的容器将占用剩余的所有空间，将页脚推到底部或将标题保留在顶部。

`Sizing with constraints`你可以利用通过基于约束的大小填充算法填充缺失值，适用于`position: absolute`的元素以及`display:block`的元素，但是后者只适用在宽度上。

如果你对绝对定位的元素设置了`width`或者`height`为`auto`，但是设置了`margin`的具体值，这样绝对定位的盒子的大小会自动调整以占据所有除了`margin`留下以外的可用空间。