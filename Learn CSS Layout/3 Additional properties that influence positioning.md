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

- 内容，例如line box中的文本
- padding或者borders，例如父元素有padding或者borders，它就不会与子元素发生margin collapse
- clearance，例如，清除浮动的结果可能使margin分离，从而不会发生margin collapse

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
