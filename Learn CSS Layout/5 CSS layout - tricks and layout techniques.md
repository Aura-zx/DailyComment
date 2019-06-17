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

### CSS layout tricks and techniques used for positioning

定位是布局的核心：或许最重要的任务是将元素放置在所有屏幕尺寸的正确相对位置。本节中的技术允许您完成此操作。

- Relative + absolute positioning
- Negative margins
- Transforms
- `margin:auto`
- Positioning with constraints

`Relative + absolute positioning`：`position:absolute`功能强大，因为你可以通过设置相对父元素的上下左右四个方向的偏移量来对齐元素。但是，在大多数情况下，您实际上并不想相对于视口定位div，您希望它相对于特定父级定位。

`Negative margins`：margins 在 CSS 里是可以设置为负数的；一般来说这个特性不是特别有用，因为当元素大小不固定时很难正确地使用这个特性。但是，它可能对一些本来很难的事情很有用。例如，如果你想让一个元素溢出 —例如图像轮播导航按钮— 设置一个固定的负边距可以故意造成溢出。另一个突出用例是主要用在旧版本的 IE 上，你讲在后面看到。

`Transforms`：CSS 3 引入了`transform:translate()`方法，它允许元素的定位根据它自己的长宽决定。这是一个使用负边距的可行替代方案。学习它是很值得的，具体而言，它允许将值表现为当前框的百分比，而不是之前 CSS 中的父盒子的百分比。例如`transforms:translateX(-50%)`将会向左移动当前盒子宽度50%的距离，对于没有固定的预定宽度的盒子，使用负边距也是不能做到的。

`margin:auto`：设置`margin:auto`在两种情况下很有用，因为这样可以使用内置算法实现居中。

- 设置`margin-left:auto`和`margin-right:auto`在块级盒子上可以让他们水平居中，还需要设置`width`来保证这个效果。
- 设置`margin:auto`和`position:absolute`可以让盒子水平和垂直居中，还需要设置`width`和`height`的值以及所有的偏移量(`top`,`left`,`bottom`,`right`)为`0`来保证这个效果。

`Positioning with constraints`：对于设置了`width`和`display:block`属性的盒子，你可以通过`position:absolute`属性做很多有趣的事情。对于绝对定位的元素，可以触发基于限制的大小计算。

例如，你想让一个盒子定位在一个父盒子的左边或右边，同时保持垂直居中，就像轮播图的左右导航按钮那样。为了达到这个效果，先将父盒子设置为`position:relative`，然后使用下面的样式：

```css
.parent {
  position: relative;
  width: 80%;
  height: 80%;
  margin: 0 40px;
}
.previous, .next {
  position: absolute;
  width: 30px;
  height: 20px;
  top: 0;
  bottom: 0;
  margin: auto;
}
.previous {
  left: 0;
  margin-left: -15px;
}
.next {
  right: 0;
  margin-right: -15px;
}
```

```html
<div class="parent blue">
  <div class="previous green">prev</div>
  <div class="next red">next</div>
</div>
```

在垂直方向上，`top:0`，`bottom:0`，`margin-top:auto`，`margin-bottom:auto`的结合触发了居中。在水平方向上，对于`.parent`div，`left:0`，`right:0`，`margin-right:auto`，`margin-left:-15px(或者 auto)`可以让盒子定位在父盒子的左边缘。`-15px`的负边距(宽度的一半)将盒子整齐的放在盒子的顶部，`.next`div同理。

