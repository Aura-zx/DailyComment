

## Box sizing in CSS

- [Box sizing in CSS](#box-sizing-in-css)
    - [Content dimensions and margins](#content-dimensions-and-margins)
    - [Box model calculations for inline elements](#box-model-calculations-for-inline-elements)
    - ["Content-based" height for blocks, floats and inline-blocks](#%22content-based%22-height-for-blocks-floats-and-inline-blocks)
    - [Width calculation](#width-calculation)
        - [Width calculations: block-level elements(constraint-based)](#width-calculations-block-level-elementsconstraint-based)
        - [Width calculations: floating blocks and inline-block elements(shrink-to-fit)](#width-calculations-floating-blocks-and-inline-block-elementsshrink-to-fit)
    - [Margins for floating blocks and inline-block elements](#margins-for-floating-blocks-and-inline-block-elements)
        - [Absolutely positioned, non-replaced elements](#absolutely-positioned-non-replaced-elements)

CSS box的计算依赖于box model，每一个box都有一个content区域以及`可选`的围绕着content区域的padding，border和margin三个部分，如图所示。

![box model](./imgs/LCL-2/box-model.png)

### Content dimensions and margins

[case 1](https://codepen.io/aura-zx/pen/QBLaoP)

`padding`和`border`的属性渲染结果一贯比较稳定，都是围绕着content区域。唯一主要的corner case是在inline的元素上（不包括inline-block），左边和右边的border只渲染一次，即使content太多折行了，上下的border会在多行都渲染，但是和block元素渲染的方式不太一致。

例子中的第一段border在block元素中，所以它渲染成一个单独、连续的box。

第二段border在inline元素中，如上面所说，它的左右border只渲染了一次，第二行上面的border在第一行下面border之上。

另外两个盒模型有趣的方面是`content dimension的计算`和`margin:auto对不同类型元素的影响`。content dimension也就是`height`和`width`的计算。
 
 如图总结了content dimension 和 margin:auto的机制。
![calculation](./imgs/LCL-2/content-dimension-and-marginauto.png)

### Box model calculations for inline elements

`inline, non-replaced元素`：`width`和`height`属性忽略，不参与计算，实际的宽和高完全由他们的内容决定。inline元素在布局中是放在line box中进行计算的，line box的尺寸基于`font-size`和`line-height`计算。`margin-top`和`margin-bottom`属性同样是忽略不计算的，但是`margin-left`和`margin-right`能正常使用，相对同一个line box中的其它元素布局。

### "Content-based" height for blocks, floats and inline-blocks

blocks, floats, inline-blocks在属性`height:auto`的情况下有相同的计算方式。`height:auto`意思是元素的高度以content决定，有两种基于content的高度计算方法：

- 一种是floats, inline-block和block元素在文档流中并且没有将`overflow`设置为visible的计算方法
- 另一种是block元素在文档流中并且`overflow:visible`的计算方法

两种计算方式将content作为高度的计算依据，但是对于`overflow:visible`的block元素，会忽略子元素中的float元素，这就是为什么当只有float子元素时，父元素的高度为0的原因。

当`overflow`的值不是visible时，float元素的高度会计算进去，标准里说

>  if the element has any floating descendants whose bottom margin edge is below the element's bottom content edge, then the height is increased to include those edges. Only floats that participate in this block formatting context are taken into account, e.g., floats inside absolutely positioned descendants or other floats are not.

也就是说，当`width`和`height`为auto时，blocks会一直扩展，直到完全包裹content，但对于float元素，仅是那些参与block formatting context的参与计算，比如absolute定位的元素就不参与。

### Width calculation

宽度的计算要更复杂些，针对`width`和`margin`值都为auto的情况有两种算法。

Block-level元素使用`constraint-based`的方法，所谓的限制也就是盒模型（比如`border`，`padding`，`margin`等属性），如果`width`或者`margin`其中有一个的值设置为auto，则这个auto的计算是通过可使用的空间减去那些已经设定好有具体值的属性得到的值作为auto最终渲染时使用的结果。

Floating block和inline-block元素使用`shrink-to-fit`的方法，它包括三个步骤，1)首选宽度（例如，尽可能少的换行符），2)首选最小可用宽度（例如，使用尽可能多的换行符），3)可用的宽度。

如果水平空间可用，则宽度值设置为`首选宽度`，否则设置为`首选最小宽度`，最坏的情况下，可用宽度可能存在一些潜在的溢出。对于floating block和inline-block元素，`margin:auto`会被解读为`margin:0`。

#### Width calculations: block-level elements(constraint-based)

标准
> 'margin-left' + 'border-left-width' + 'padding-left' + 'width' + 'padding-right' + 'border-right-width' + 'margin-right' = width of containing block

这其中`border`，`padding`的属性不能设置为auto，它们必定有一个确定的值，上面的式子可以化简为

> margin-left + width + margin-right = width of containing block

当显式的设置这三个属性的值时，可以直接使用这个计算结果。当发生以下三种情况时，使用constraint-based方法

- `width`是auto且`margins`是auto
- `width`是具体的值且`margins`是auto
- 三个值中有两个是具体的值，另一个是auto

[case1](https://codepen.io/aura-zx/pen/RBarbJ)

当`width`设置为auto时，其它设置为auto的属性变为`0`，在case 1中box的width为整个行的宽，包括了所有padding和border需要的空间。

[case2](https://codepen.io/aura-zx/pen/xJOwjw)

当`width`有具体的值，`margin`为auto时，将box居中。这里隐含的计算是，当`margin-left`和`margin-right`都为auto时，他们的值相等，所以就将box居中了。当然只是水平居中，因为block formatting context 将box从上至下顺序排布，所以不能垂直居中。

[case3](https://codepen.io/aura-zx/pen/wxWMKR)

当三个值中有两个是具体值时，剩下的那个值等于可用宽度减去那两个特定值。

#### Width calculations: floating blocks and inline-block elements(shrink-to-fit)

在了解`shrink-to-fit`算法之前首先了解几个概念：

1. `perferred width(pw)`使换行符最少的宽度
2. `perferred minimum width(pmw)`使换行符尽可能多的宽度，CSS 2.1并没有指定计算这个宽度的算法
3. `available width(aw)`父元素的宽度减去水平方向margin，border，padding的值以及可能存在的滚动条的宽度

shrink-to-fit算法就可以描述为min(max(pmw, aw),pw)。

[case1](https://codepen.io/aura-zx/pen/QBEype)

perferred width值作为宽度的例子。

[case2](https://codepen.io/aura-zx/pen/vaXNVg)

当available width小于preferred width，但是大于等于preferred minimum width，使用available width作为宽度的例子。

[case3](https://codepen.io/aura-zx/pen/bjwVZM)

当available width小于preferred minimum width，使用available width作为宽度的但是会有溢出的例子。

### Margins for floating blocks and inline-block elements

floating blocks和inline-block元素的margin计算十分简单，任何被设置为`auto`的magrin都将置为0。

#### Absolutely positioned, non-replaced elements

absolutely positioned元素将constraint-based，shrink-to-fit和content-based三种算法结合在一起来定位水平和垂直位置。

高度的计算：

> 'top' + 'margin-top' + 'border-top-width' + 'padding-top' + 'height' + 'padding-bottom' + 'border-bottom-width' +
'margin-bottom' + 'bottom'
= height of containing block

宽度的计算：

> 'left' + 'margin-left' + 'border-left-width' + 'padding-left' +
'width' + 'padding-right' + 'border-right-width' + 'margin-right' +
'right'
= width of containing block

同样，我们可以忽略padding和borders，因为他们不能被设置为auto，值要么为0，要么为一个特定的值。所以上面的可以简化为

height = 'top' + 'margin-top' + 'height' + 'margin-bottom' + 'bottom'

width = 'left' + 'margin-left' + 'width' + 'margin-right' + 'right'