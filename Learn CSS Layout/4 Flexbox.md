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

### display:flex and anonymous box generation

首先，如何触发`flexbox`布局以及它适用于哪些元素？

`flexbox`布局影响每一个父元素下面所有的直接子元素。要触发`flexbox`布局，将父元素设置为`display:flex`。该父项的每个子元素都将使用`flexbox`布局。

> ​	与块级格式化上下文和内联格式化上下文的工作方式类似，flex容器的每个子元素都变为flex项；如果有必要，对于没有包含元素的文本内容匿名盒，每一个in-flow子项都变为flex项，并且直接包含在flex容器内的每个连续文本行都包含在匿名flex项中。但是，不会渲染仅包含空格的匿名flex项(例如，可能受空白属性影响的字符)，就好像它是`display:none`。[source](https://www.w3.org/TR/css-flexbox-1/#flex-items)

但是，absolute定位的子项被排除在外。

> ​	flex容器中的absolute定位的子项不会在flex布局中。但是它却会被`order`属性影响，影响到它被渲染的顺序。[source](https://www.w3.org/TR/css-flexbox-1/#abspos-items)

一个in-flow子项变成flex项意味着什么？

> flex项的`display`值会被阻塞：如果生成flex容器的元素的in-flow子节点的`display`值是内联级别的值，则计算块级别的等效值。
>
> 一些`display`值会触发生成一个围绕原盒子的匿名盒。它是最外层的盒子-flex容器的直接子盒，会成为一个flex项。例如两个相邻的`display:table-cell`子元素，包裹它们的匿名table盒子会成为一个flex项。

总结一下，所有的`display:inline-*`的子元素都会被当做`display:block`，例如，`display:inline-table`会变成`display:table`；flex项的尺寸取决于它基于的最外层盒子。

### Flex container properties: main and cross axis

Flexbox 父项可以决定子项是水平或者垂直布局。你可以按照自己需要切换它们的方向，flexbox的标准使用了`main axis`和`cross axis`来命名，子项堆叠的方向称为主轴，垂直于主轴的轴称为横轴

![main axis and cross axis](../imgs/LCL-4/main axis and cross axis.png)

`flex-direction`属性控制着主轴的方向。默认值是`flex-direction:column`，其它可能的值如下图所示

![flex-direction](../imgs/LCL-4/flex-direction value.png)

### Flex container properties: flex lines

就像内联格式化上下文一样，flex容器的内容可以布局在很多行上。

> Flex items in a flex container are laid out and aligned within *flex lines*, hypothetical containers used for grouping and alignment by the layout algorithm.

`flex-wrap`属性控制着子元素是否包裹或者溢出。

> A flex container can be either single-line or multi-line, depending on the flex-wrap property:
>
> - A *single-line* flex container lays out all of its children in a single line, even if that would cause its contents to overflow.
> - A *multi-line* flex container breaks its flex items across multiple lines, similar to how text is broken onto a new line when it gets too wide to fit on the existing line. When additional lines are created, they are stacked in the flex container along the cross axis according to the flex-wrap property. Every line contains at least one flex item, unless the flex container itself is completely empty. [source](http://www.w3.org/TR/2015/WD-css-flexbox-1-20150514/#flex-lines)

![flex-wrap](../imgs/LCL-4/flex-wrap.png)

### Flex items: flex item sizing

为了理解 flex 项如何在 flex lines 上分布的，需要先知道他们的尺寸是如何计算的。[flex标准中Section 9](https://www.w3.org/TR/css-flexbox-1/#layout-algorithm) 描述

了布局算法的细节，但就我们的目的而言，有趣的是要注意定位按照下面的顺序执行：

1. 使用`flex-basis`计算容器的大小和每个flex项目的初始大小
2. 将 flex 项分配给 flex lines，(如果有`flex-warp`属性)
3. 每个 flex 项的最终大小由`flex-grow` 和`flex-shrink`计算得出
4. flex lines 在主轴上对齐(`justify-content`)
5. flex lines 在副轴上对齐(`align-items`,`align-content`和`align-self` )

换句话说，这个顺序中有三个高级步骤：

- `将项目分配到flex lines`：计算 flex 项目的初始大小，并根据这个大小在 flex lines 上划分项目
- `在每个flex line上面重新计算flex项的尺寸`：在每条line上计算每个item的最终大小
- `对齐lines和items`：先是lines的对齐，然后是items的对齐

### flex-basis

`flex-basis`是理解 flexbox 布局是如何计算的关键属性，因为它会影响 flex 项如何分割到其它线上以及之后发生的 flex 计算。

`flex-basis`属性作用在 flex 项上(它也可以继承自父元素)

合法的值：

> - *auto*: When specified on a flex item, the auto keyword retrieves the value of the main size property as the used flex-basis. If that value is also auto, then the used value is content.
> - *content*: Indicates automatic sizing, based on the flex item’s content.
> - *width*: For all other values, flex-basis is resolved the same way as width in horizontal writing modes: percentage values of flex-basis are resolved against the flex item’s containing block, i.e. its flex container, and if that containing block’s size is indefinite, the result is the same as a main size of auto. Similarly, flex-basis determines the size of the content box, unless otherwise specified such as by box-sizing. [source](http://www.w3.org/TR/2015/WD-css-flexbox-1-20150514/#flex-basis-property)

正如标准里描述的，`flex-basis`属性控制弹性项目的初始主要大小，这一过程是在根据弹性因子分配自由空间之前的。

> specifies the flex basis: the initial main size of the flex item, before free space is distributed according to the flex factors. [source](http://www.w3.org/TR/2015/WD-css-flexbox-1-20150514/#flex-basis)

本质上，`flex-basis`如果有具体的值，那么在 flex 相关的计算中作用是替代`width`或`height`的角色，所谓具体的值就是px或者百分比(相对于父元素)。

如果`flex-basis`的值被设置为`auto`或者`content`后，则与 flex 相关的计算可以通过并使用在元素本身上设置的值，或通过基于内容的计算得出。标准提供了以下说明：

![flex-basis](../imgs/LCL-4/flex-basis.png)

但我认为从四种不同的弹性尺寸操作模式(我提出的术语，并不是标准术语)方面更容易理解`flex-basis`。

`purely proportional`：纯比例运算模式，设置`flex-basis:0`相当于将子元素的默认`width`或者`height`为`0px`，换句话说：

- flex 项目内容的实际宽度对元素计算的最终大小没有任何影响
- flex 项永远不会换行，因为关于何时换行到新行的决定是基于子项的 flex 基本大小完成的，并且显式设置的[确定值](https://www.w3.org/TR/css-flexbox-1/#definite)优先于 flexbox 算法

`fixed basis + proportional`：固定基数+比例运算模式，设置`flex-basis: Npx`，`N`会导致在计算元素大小时将这些元素视为具有`N`个像素的 flex 的基本大小。

- 当 flex 项的 flex base值的总和大于 flex 容器可用的主轴幅度，则会发生换行
- 当计算`flex-grow`和`flex-shrink`时，将比例增长或收缩与`Npx`的基础尺寸相加或相减

`auto basis + proportional`：自动基础 + 比例运算模式，设置`flex-basis:auto`，`flex-basis`的真实值回落到元素的`width`或`height`上，如果他们没有被指定，则回落到通常用于计算`width`和`height`的算法上。

- 当做出需要换行的决定时，flex 项目是需要有确定的`width`和`height`的，如果没有，则大小基于内容
- 当计算`flex-grow`或`flex-shrink`时，将比例增长或收缩与基础宽/高或者计算后的宽/高相加或相减

`content basis + proportional`：内容基础 + 比例运算模式，设置`flex-basis:content`，计算 flex 项目的基础大小就好像底层元素具有`width:auto`和`height:auto`。

- 关于换行和`flex-grow`、`flex-shrink`的决定都是在设置`width:auto`和`height:auto`计算的值上运行的
- 目前`flex-basis:content`非常新，在Chrome和Firefox都不支持

现在已经了解了`flex-basis`属性是什么以及如何影响 flexbox 的计算，接下来看一下 flex 项目是如何分割到 flex lines 上的。

### Dividing flex items onto flex lines

flex 项被分割到不同的 flex line 上是基于`hypothetical main size`，在标准的[9.3节](http://www.w3.org/TR/css3-flexbox/#main-sizing)有具体的描述，将 flex 项目分割到 flex lines 上的过程非常简单：

> Collect flex items into flex lines:
>
> - If the flex container is single-line, collect all the flex items into a single flex line.
> - Otherwise, starting from the first uncollected item, collect consecutive items one by one until the first time that the next collected item would not fit into the flex container’s inner main size (or until a forced break is encountered, see §10 Fragmenting Flex Layout). If the very first uncollected item wouldn’t fit, collect just it into the line.
>
> For this step, the size of a flex item is its outer hypothetical main size.
>
> Repeat until all flex items have been collected into flex lines.

这里面唯一 tricky 的地方是如何理解`outer hypothetical main size`。

[标准9.3](http://www.w3.org/TR/css3-flexbox/#algo-main-item)中描述了这一过程的细节，简单地说，如果`flex-basis`被设置了确定的值(比如具体的像素值)，这就是 flex 的基本大小，通常这是盒子在主轴上可以采用的适应其内容的最小尺寸。

`hypothetical main size`的计算是通过将`min-width`,`min-height`,`max-width`,`max-height`用作约束于 flex basis 得出的值。

> The hypothetical main size is the item’s flex base size clamped according to its min and max main size properties.

那一节的细节还是比较纷繁复杂的，有一个简单的方法可以去看这个值在实际是怎么实现的：设置`flex-grow`和`flex-shrink`为`0`去禁止`resizing`行为和设置`width/height`为`0`。这可以让你直观地看到每一个子元素的主轴尺寸的计算。由于换行发生在任何增长或收缩之前，因此在启用增长或收缩后，换行的断点相同。

请注意，自从最初撰写本章以来，规范发生了变化，最初的版本中，内在大小的规则是：

> The main-size min-content/max-content contribution of a flex item is its outer hypothetical main size when sized under a min-content/max-content constraint (respectively)

但是2015年之后：

> The main-size min-content/max-content contribution of a flex item is its outer min-content/max-content size, clamped by its flex base size as a maximum (if it is not growable) and/or as a minimum (if it is not shrinkable), and then further clamped by its min/max main size properties.

现在，代替了`hypothetical outer main size`， 在`flex-grow:0`/`flex-shrink:0` 的条件下，flex 项的值是`min(max(content-size, flex-basis), max-width/max-height)` / `max(min(content-size, flex-basis), min-content-size)`。

下面的例子展示出了四种不同的操作，从左至右分别是：

- `flex-basis: 0` with `width: 45px` on each flex item results in the items having at least a `0px` width, or their content size if it is greater.
- `flex-basis: 10px` with `width: 45px` on each flex item results in the items having at least a `10px` width, or their content size if it is greater.
- `flex-basis: 35px` with `width: 45px` on each flex item results in the items having at least a `35px` width, or their content size if it is greater.
- `flex-basis: 35px` with `width: 45px` and `max-width: 10px` on each flex item results in the items having a `10px` width.
- `flex-basis: auto` with `width: 45px` on each flex item results in the items having a `45px` width, and the items wrap because the sum of flex basis sizes exceeds the flex container's width.
- `flex-basis: content` with `width: 45px` on each flex item should result in the flex items being sized exactly to their content, but this value is not supported as of the time I'm writing this.

![flex-lines-examples](/Users/zhouxin/Code/DailyComment/imgs/LCL-4/flex-linex.png)

```html
<div class="flex-parent blue">
  <div class="green">aaa</div><div class="orange">bbbb</div><div class="red">ccc</div>
</div>
```

```css
.flex-parent {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  flex-grow: 0;
  justify-content: flex-start;
  width: 150px;
  height: 100px;
}
```

现在，我们已经看到项目是如何放置在 flex lines 上的，接下来我们看一下`flex-grow`和`flex-shrink`属性以及相关的计算工作。