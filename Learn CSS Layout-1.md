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

inline formatting 就比较复杂了，因为它涉及到将内容分割成line box(将那些在一行的boxes的长方形区域称为`line box`)，正常来说inline的元素时在一行中平行布局，当一行放不下时，会进行垂直布局，此时是以line box为单位垂直布局。

inline box的另一个特点是给它设置`width`、`height`这些属性时会被忽略，当通过设置`position:absolute`使它成为block时才会使这些属性生效。
TODO:当被container有width时，[inline的布局](https://codepen.io/aura-zx/pen/eKJXQQ)。

另外，`display:inline`和`display:inline-box`的区别可以见[这个问题](https://stackoverflow.com/questions/8969381/what-is-the-difference-between-display-inline-and-display-inline-block)。