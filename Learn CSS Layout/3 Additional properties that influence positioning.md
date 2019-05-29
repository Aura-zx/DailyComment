本章主要讨论盒模型中的属性如何在不同元素类型之间变化。

- `Margin collapsing`影响相邻边距，仅应用两个中间更大的那个
- `Negative margins`.负边距是允许的，它可以帮助定位内容
- `Overflow`.当容器框内的内容大于其父级或位于父级框之外的偏移量时，它就溢出了，这个属性控制这种情况下的表现
- `max-width, max-height, min-width, min-height`，`width`和`height`可以更进一步的通过这四个属性控制
- `Pseudo-elements`伪元素可以伴随选中的CSS元素生成，它生成的额外的内容通常用来布局或者提供额外的视觉表现
- `stacking contexts and rendering order`定义盒子的z轴渲染

## 1 Margin collapsing

边距之间有一个特别的交互：相邻垂直方向的两个margins将会合并为一个，被称作`margin collapsing`，这里的合并一般是直接选择哪个更大的值作为合并结果。

[case 1](https://codepen.io/aura-zx/pen/QBLaoP)

这个例子中5px和10px之间的margin是10px，10px和20px之间的margin是20px，这就是`margin collapsing`，渲染应用值更大的那个。

这里是4条high-level的规则来限制margin是否collapse：

1. 只有垂直方向的margin会collapse，水平方向的不会。
2. 只有block-level元素的margin会collapse，float，absolute和inline-block的元素不会collapse，inline-level的元素没有margin所以它也不会发生collapse。
3. 只有在相同的块级格式化上下文中的boxes的margin会发生collapse。
4. 只有相邻的margins会发生collapse。

标准中对于“相邻”的定义有些迂回，但是我发现只要将margin-top和margin-bottom作为长方形自己的值，忽略那些生成它们的矩形，然后看他们是不是相互接触。

当你讲margins当做盒子本身，下面这些情况它们都是相邻的：

- 父子元素的margins
- 兄弟元素的margins
- 祖父元素、父元素和子元素的margins
- 没有内容的元素的margins

这些条件都是符合margin collapse，但是他们不能被下面这些情况分割：

- 内容 (例如line box中的文本)
- padding或者borders (例如父元素有padding或者borders，它就不会与子元素发生margin collapse)
- clearance (例如，清除浮动的结果可能使margin分离，从而不会发生margin collapse)

标准里说margin collapse会发生这些事

> When two or more margins collapse, the resulting margin width is the maximum of the collapsing margins' widths. In the case of negative margins, the maximum of the absolute values of the negative adjoining margins is deducted from the maximum of the positive adjoining margins. If there are no positive margins, the maximum of the absolute values of the adjoining margins is deducted from zero.

提到了margin有负值的情况，此时最大值的计算是正值中的最大值减去负值中绝对值最大的结果，当没有正值的时候，用0作为正值中的最大值。

maxVal = (max(正值) || 0) - max(|负值|)

[case 2](https://codepen.io/aura-zx/pen/BPbGVz)

[case 3](https://codepen.io/aura-zx/pen/RBdqBw)

[case 4](https://codepen.io/aura-zx/pen/rrRQrv)

case 2，case 3和case 4分别体现了不同情况下的margin collapse和没有margin collapse的情况。

- case 2中是兄弟元素间，父元素和子元素间的margin collapse，注意第一个"Hello world"文本和页面顶部的间距也只有10px
- case 3中给每一个div增加了`overflow:auto`属性，这意味着每一个div有自己的块级格式化上下文。所以兄弟元素间依旧会发生margin collapse，但是父元素和子元素之间就不会发生了，所以第一个"Hello world"文本在这个例子中是和父元素的顶部间距为10px，距离页面顶部则有20px
- case4中给每一个div增加了border属性，这种情况下，第一个"Hello world"文本距离页面顶部有30px，因为margin是不再是相邻的，被border分割开了，所以body元素没有发生margin collapse，这个例子中只有兄弟元素间发生margin collapse

## 2 Negative margins

Negative margins 仅仅在标准中被提到，但是他在布局中能起到很特别的作用。

标准的描述

> In the case of negative margins, the maximum of the absolute values of the negative adjoining margins is deducted from the maximum of the positive adjoining margins.If there are no positive margins, the maximum of the absolute values of the adjoining margins is deducted from zero.

负边距会改变渲染发生的位置，可用于重新定位内容。它们曾经是实现某些结果的唯一方法，例如在过去不好的日子里实现居中。问题在于负边距与它们重叠的内容没有特性相互作用，这导致了内容间意外地相互重叠很难调试。如今这样的技巧很少见了，这得益于更好的替代手段的发展。

## 3 Overflow

Overflow发生在当子元素被定位在它的父元素之外或者子元素不适于父元素的维度。`overflow`属性控制如何呈现溢出子节点的部分。

> This property specifies whether content of a block container element is clipped when it overflows the element's box. It affects the clipping of all of the element's content except any descendant elements (and their respective content and descendants) whose containing block is the viewport or an ancestor of the element.

`overflow`属性可以取下面几个值，默认值为`visible`，任何`visible`之外的值都会创建一个BFC。

- `visible`：内容不会被剪切，比如，它可能被渲染到block box之外
- `hidden`：内容会被剪切，而且没有滑动条
- `scroll`：内容会被剪切，用户代理(UA)使用屏幕上可见的滚动机制(例如滚动条或平移器)，并且无论是否剪切任何内容，都应该为该框显示该机制
- `auto`：auto的行为是UA相关的，但是应该为溢出的盒子提供滚动机制

## 4 max-width, max-height, min-width, min-height

为了能更明确地设置`width`和`height`，CSS 2.1通过`max-width`,`max-height`,`min-width`,`min-height`四个属性来限制`width`和`height`。

它们的取值既可以是明确的单位例如`px`，也可以是父元素`width`和`height`的百分比。它们的取值为具体的数字时时很直观的，但是如果为百分比则有需要注意的地方

- `max/min-width`：会根据包含它块的宽度计算，如果是该宽度为负值，则计算结果是0；如果包含它的块的宽度是依赖于此元素，则结果在CSS 2.1中是未定义的
- `max/min-height`：会根据包含它块的高度计算，如果该高度不被明确指定，而且这个元素也不是`absolute`定位的，则计算结果视为0或者none

也就是说，对于这四个属性，百分比的计算结果只会在它们父元素对应的属性有明确而具体的值的时候才会实现。那么它具体的计算是这样的

> max-width/min-width属性的算法
>
> - 先暂时计算`width`属性的值，使用之前描述过的"计算width和margin的方法"
> - 如果计算出来的值大于`max-width`，那么使用`max-width`的值作为`width`的值
> - 如果计算出来的值小于`min-width`，那么使用`min-width`的值作为`width`的值
>
> max-height/min-height属性的算法
>
> - 先暂时计算`height`属性的值，使用之前描述过的"计算height和margin的方法"
> - 如果计算出来的值大于`max-height`，那么使用`max-height`的值作为`height``的值
> - 如果计算出来的值小于`min-height`，那么使用`min-width`的值作为`height`的值

有一个很细微的点，当父元素的`height`小于子元素，那么设置`height:100%`和设置`min-height:100%`会造成完全不同的结果。

[case: height && min-height](https://codepen.io/aura-zx/pen/NVLZdB)

- `height:100%`会使子元素的高度匹配父元素的高度，内容就会溢出
- `min-height:100%`会使子元素高度匹配内容

这种现象出现的原因是设置`min-height`使得`height`的值为默认的`auto`。然后开始正常的盒模型计算，block level的元素会让它的大小适用于内容。另一方面设置`height:100%`不会触发content-base的尺寸计算，它只会直接的将`height`设置为某个固定的值。

不过，当你设置的内容太短不能填充所有的高度，那么`min-height`将会被设置为父元素的高度。