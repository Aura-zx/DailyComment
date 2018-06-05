> 从今天开始学习CSS Layout

主要跟着这本书学习[Learn CSS Layout](http://book.mixu.net/css/)

## 1 Box positioning in CSS

CSS布局的核心就是把HTML的元素映射一些`rectangular boxes`，这些盒子又分布在x、y、z三个轴上，x,y方向上的位置是由`positioning scheme`定义的，在CSS 2.1中定义了三种：`normal flow`，`floats`，`absolute positioning`。

### Positioning Scheme

- `normal flow`：包括三种`fomatting context`，**block, inline, relative**formatting context。
- `floats`：以自己的方式和normal flow进行交互，并形成了大多数现代CSS grid框架。
- `absolute positioning`：作用是相对于normal flow的绝对位置和固定的元素。

涉及这些的CSS的属性是`display`，`position`，`float`。float和absolute定位可以看做是和normal flow的交互，也比较复杂，所以首先了解normal flow。从设计角度layout主要做了两件事：

- 元素盒子的尺寸和对齐是如何处理的，这些一般是通过display的属性(width,height,margin)来完成的
- 有相同父元素的的子元素是如何相互定位的

这章主要讨论第二件事。

标准是这样定义formatting context，即块级元素就在一个block formatting context，行级元素就在inline formatting context。

> Boxes in the normal flow belong to a formatting context, which may be block or inline, but not both simultaneously. Block-level boxes participate in a block formatting context. Inline-level boxes participate in an inline formatting context.

从视觉上看block formatting context就是一个垂直的栈布局，inline formatting context就是一个水平的栈布局。

### Anonymous box generation

当一个父级元素同时包含inline和block元素时，会进行`Anonymous box`的生成，具体规则是：

- 当同时有inline和block元素存在时，会生成`Anonymous block boxes`，并将inline元素放进去。
- 当有inline元素且这个元素被text包裹时，会生成`Anonymous inline boxes`，并将text放进去。

需要注意的是，这些Anonymous box是不会再chrome等浏览器的布局中显示的，参考[这个问题](https://stackoverflow.com/questions/16823693/inline-anonymous-boxes)。

### Normal flow positioning

#### block formatting

block formatting 规则比较简单，如图它遵循这样一些规则

![block box](./imgs/LCL-1/block-box.png)

- 每个block box在父容器的左外边缘
- float不会影响左外边缘的位置，但会影响文字的位置
- 没有设置宽度的block box会填充满整个父容器的宽度
- 设置了宽度的会从左边开始计算
- 宽度即使没有充满父容器，block box也不会同一行

#### inline formatting

inline formatting 就比较复杂了，因为它涉及到将内容分割成line box(将那些在一行的boxes的长方形区域称为`line box`)，正常来说inline的元素时在一行中平行布局，当一行放不下时(这里的行宽度由它的父元素决定)会进行垂直布局，此时是以line box为单位垂直布局。

inline box的另一个特点是给它设置`width`、`height`这些属性时会被忽略，通常情况下inline元素的宽度由它的父元素决定。当通过设置`position:absolute`使它成为block时才会使这些属性生效。

对于line box，它的高度可以由`line-height`控制，以绝对高度或者相对高度的形式，绝对高度就是一个固定的值，或者以倍数于当前元素设置的`字体尺寸`来决定，可大可小，默认情况下根据字体的尺寸来决定行高。line box的高度是在计算所有inline box之后进行的，然后设置除过`vertical-align:top/bottom`，之外的其它vertical-align属性。[例子](https://codepen.io/aura-zx/pen/zaqyaY)。

当container有width时，[inline的布局](https://codepen.io/aura-zx/pen/eKJXQQ)像是block元素，实际上只是因为width太小，导致每一个inline元素变成了上一段说的line box。

垂直居中是一个常见的需求，[这个例子](https://codepen.io/aura-zx/pen/MXyLoa)通过将需要垂直居中的元素设为`display:inline-box`，`vertical-align:middle`解决了水平方向居中的问题，配合一个container后面的样式为`height:100%`的伪元素将line box的高度和container一致，这样该元素就可以和这个伪元素对齐，解决了垂直方向居中的问题。

`display:inline`和`display:inline-box`的区别可以见[这个问题](https://stackoverflow.com/questions/8969381/what-is-the-difference-between-display-inline-and-display-inline-block)。简单说就是inline-box可以设置width和height，但又不会像block一样独占一行。

[这个例子](https://codepen.io/aura-zx/pen/BVKbmK)介绍了当两个元素中间存在空格时会生成一个匿名的inline box导致两个本该在一行的inline box分别在两行。
有几种办法可以解决:

- 手动去掉那个空格，但这样做会影响代码可读性
- 将父容器的`font-size:0`，再单独给两个子元素设置`font-size`，这可以使那个空格不再占用任何空间
- 父容器设置`white-space: nowrap`，虽然两个子元素在一行了，但是空格依旧存在
- CSS3的`text-space-collapse`属性，缺点是目前许多浏览器暂不支持

