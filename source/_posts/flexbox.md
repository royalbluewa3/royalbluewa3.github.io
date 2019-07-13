---
title: flex 布局速记
categories: FE
tags:
  - flex
  - layout
abbrlink: 7b915b90
date: 2019-07-12 16:16:31
---


flex 弹性布局使得项目能够弹性地改变其宽高，自由填充容器剩余空间或压缩以适应容器主轴空间，同时还可方便地调节项目方向和顺序。flex 布局常用于项目宽高需要自适应或成比例的场景。


## 基本概念


![基本概念](https://i.loli.net/2018/05/19/5aff0818e19da.png)
<!-- more -->

1. 如图所示，在弹性盒模型里，有容器和项目之分，容器可统一设置子项目的排列方式，子项目也可以单独设置自身的排列方式，两者同时设置时，以项目的设置为准。

2. 容器中存在两轴，主轴 `main axis` 和交叉轴 `cross axis` ，主轴默认水平方向，交叉轴默认垂直方向。可通过修改容器 `flex-direction` 的属性值设置主轴为垂直方向，交叉轴为水平方向。

3. 主轴起始位置为 `main start`，末尾位置为 `main end`；交叉轴起始位置为 `cross start`，末尾位置为 `cross end`。

4. 容器中的项目 (flex item) 占据的主轴空间为 `main size`, 占据的交叉轴空间为 `cross size`。默认情况下，`main size` 为项目宽度，`cross size` 为项目高度；主轴垂直方向时，两者互换。

5. flex 布局定义了新的格式化上下文 FFC (flex formatting context)，类似 BFC。例如不会被浮动的元素遮盖，垂直外边距不会坍塌等。

6. 项目尺寸 (item-size) 为主轴方向上项目的 `content + padding + border + margin`
<!-- more -->

## 指定容器

实现 flex 布局首先需指定一个容器：
```
.container {
    display: flex | inline-flex;
}
```
如此一来，元素将升级为弹性盒，前者为块级盒，后者为行内盒。 

元素采用 flex 布局后, 子元素的 `float`、`clear`、`vertical-align`、`::first-line和::first-letter` 以及多栏布局模块的 `column-*` 属性都将失效。


## 容器属性

容器可设置以下 6 个属性：

- `flex-direction` 指定主轴的方向

flex-direction | 描述
:----: | :----: 
row（默认） | 主轴水平方向，子项目从左至右 →
row-reverse | 主轴水平方向，子项目从右至左 ←
column | 主轴垂直方向，子项目从上至下 ↓
column-reverse | 主轴垂直方向，子项目从下至上 ↑


- `flex-wrap` 指定如何换行
    默认情况下，项目都排在主轴线上，使用 `flex-wrap` 实现项目换行。

flex-wrap | 描述
:----: | :----: 
nowrap（默认） | 不换行，即当主轴尺寸固定时而当空间不足时，项目尺寸会随之调整
wrap | 主轴上项目总尺寸超出容器时换行，第一行在上方
wrap-reverse | 换行，第一行在下方

- `flex-flow` 前面两个属性的简写形式，默认值为 `row nowrap`


- `justify-content` 指定项目在主轴上的对齐方式

justify-content | 描述
:----: | :----: 
flex-start（默认） | 起始位置与主轴起点对齐
flex-end | 末尾位置与主轴终点对齐
center | 居中
space-between | 两端对齐，项目之间的间隔相等
space-around | 各项目间隔相等，且该距离为最左、右侧项目与容器边缘间隔的 2 倍


- `align-items` 指定项目在交叉轴上的对齐方式

align-items | 描述
:----: | :----: 
stretch（默认） | 未设置高度或者设为 auto，将占满容器高度
flex-start | 起始位置与交叉轴起点对齐
flex-end | 末尾位置与交叉轴终点对齐
center | 基于交叉轴的中点对齐
baseline | 基于首行文字的基线对齐


- `align-content` 指定多根轴线的对齐方式，即当 `flex-wrap` 设置为 `wrap` 时，容器可能会出现多条轴线，此时需要设置该属性。若项目只有一根轴线，则该属性不起作用

align-content | 描述
:----: | :----: 
stretch（默认） | 多根主轴上的项目充满交叉轴，适用于项目未设置高度
flex-start | 项目起始位置均与交叉轴起点对齐
flex-end | 项目末尾位置均与交叉轴终点对齐
center | 在交叉轴方向居中于容器
space-between | 与交叉轴两端对齐, 间隔全部相等
space-around | 各主轴项目间距相等，且该距离为顶部、底部主轴项目与容器边缘间隔的 2 倍


## 项目属性

子项目可设置以下 6 个属性：

- `order` 
    
    指定项目的排列顺序，覆盖 HTML 结构，数值越小，顺序越靠前，可为负值，默认为 0。

- `align-self` 
    
    允许单个项目有与其它项目不同的对齐方式，覆盖容器 `align-items` 定义的值。该属性共有 6 种值，与 `align-items` 保持一致，默认为 auto。

- `flex-basis`
    
    指定项目占据的主轴空间，浏览器会根据该属性计算主轴是否有剩余空间。一般情况下，优先度高于 `width` 或 `height`，默认值为 `auto`，即取项目明确设定的宽高，若宽高未明确设置，则取值依据项目实际内容决定。

    **当设定 `flex-basis` 值为 0% 时，项目的基准空间为 0。**
    
    相关计算的详细规则，请查看【flex-item-size 计算的相关扩展】 一节。  

- `flex-grow`
    
    指定项目的放大比例，值可取任何正整数，默认为 `0`，即存在剩余空间也不放大。假设各项目的放大比例数值之和为 `n`， 则容器剩余空间将分为 `n` 份, 各项目分配到 `x/n` 份（变量 `x` 为某项目的放大比例）。

- `flex-shrink`
    
    指定项目的坍缩比例，当容器主轴剩余空间不够，且未设置 `flex-wrap：wrap | wrap-reverse` 时，`flex-shrink` 生效。默认为 1，表示可坍缩；设置为 0 时，即便空间不足项目也不坍缩。取值为非负数，负值无效。
    
    在计算元素坍缩的实际数值时，需加权项目原本宽高，以图例说明：

    ![flex-shrink](https://i.loli.net/2018/06/02/5b129688e69ed.png)

- `flex` 
    
    `flex-grow`、`flex-shrink`、`flex-basis` 三个属性的简写组合，**建议优先使用该属性**，而非分别设置三属性。默认值为 `0 1 auto`。
    即，项目尺寸原本宽高，不放大，空间不够时按比例缩小。
    
    常见设定：
    1. `flex: auto` → `flex: 1 1 auto`
    
    2. `flex: none` → `flex: 0 0 auto`
    
    3. `flex: n` → `flex: n 1 0%`
        n为非负数字

    4. `flex: k` → `flex: 1 1 k`  
        k为百分比%（相对于父容器而言）或长度px/em/vw/vh，注意 `0%` 是一个百分比而非一个非负数字
    
    5. `flex: x y` → `flex: x y 0%`
    
    6. `flex: n k` → `flex: n 1 k`


**综合以上特性说明  `flex-grow`、`flex-shrink` 的生效情况：**

1. 当项目默认宽度和小于容器宽度时，`flex-grow` 生效

2. 当 `flex-wrap` 为 `wrap | wrap-reverse`，且项目默认宽度和大于容器宽度时，项目会换行，换行后每一行都可能有剩余空间，这时 `flex-grow` 生效；若当前行所有子项的 `flex-grow` 都为 0，则剩余空间保留，若当前行存在部分项目的 `flex-grow` 不为 0，则剩余空间会被这些项目占据

3. 当 `flex-wrap` 为 `nowrap`，且项目宽度和超过容器宽度时，`flex-shrink` 生效。当该行所有项目的 `flex-shrink` 都为 0 时，将出现横向滚动条


## flex-item-size 计算的相关扩展

为方便说明均以水平方向主轴为例：

1. 元素存在默认宽高
    
    如果项目有默认固定宽高（如 `<input>` 标签），且设置了 `flex-basis`，那么它的宽度将默认固定宽作为下限，若 `flex-basis` 大于默认固定宽，以 `flex-basis` 为准，反之则以默认固定宽为准。因此若想要实现 `<input>` 宽度完全自适应，可以设置其 `width` 为 0。

2. 元素存在 `min-width` 或 `max-width`
        
    类似的，`min-width` 值作为下限，若 `flex-basis > min-width`，项目宽度以 `flex-basis` 为准；若 `flex-basis < min-width`，项目宽度以 `min-width` 为准。如果 `min-width` 超出容器范围，那么即使设置了 `flex-shrink` 也不会对其进行压缩。
        
    反之，如果有 `max-width` 的限制，那么 `flex-basis` 无法超过这个值，在 `flex-grow` 生效情况下也至多只会扩大到 `max-width` 上限。

3. `width: auto`

    前面提到，如果项目同时设定了 `width` 和 `flex-basis`，`flex-basis` 的优先度高可直接覆盖 `width` ，这是通常情况，如下图：

    ![sample](https://i.loli.net/2018/06/02/5b129f09d92bc.png)
    
    但当设定了 `width: auto;` 时，需分情况讨论： 
    
    项目中存在内容，且内容撑开的宽度大于容器宽度，则实际宽度以内容确定，且无法坍缩，此时 `flex-basis` 无效；若内容撑开的宽度小于容器宽度，则项目实际宽度由内容与 `flex-basis` 中的较大值确定：
    
    ![flex-basis01](https://i.loli.net/2018/06/02/5b129dec574ce.png)

    上下两张图的区别在于，内容撑开宽度分别大于和小于 `flex-basis`，但项目均未坍缩。

    ![flex-basis02](https://i.loli.net/2018/06/02/5b129e45b5b01.png)

    此外 `overflow: hidden` 也会对项目实际宽度有所影响，如下图，文字长度被限制在容器内，小于 `flex-basis`，项目可坍缩。

    ![overflow: hidden](https://i.loli.net/2018/06/02/5b129767833f7.png)

4. `max-width` 限定下的 `flex-grow`
    
    直接上图：

    ![max-width/flex-grow](https://i.loli.net/2018/06/02/5b129f7b18cef.png)

5. `min-width` 限定下的 `flex-shrink`

    直接上图：

    ![min-width/flex-shrink](https://i.loli.net/2018/06/02/5b129f8b6dc83.png)

6. 隐藏属性对 items-size 的影响
    
    设置 `visibility: hidden; | visibility: collapse; | transform: scale;`项目宽度仍会被算进主轴，flex 有效；设置 `display: none;`，flex 失效。

7. 绝对定位与 flex 
    
    由于脱离文档流，绝对定位元素不参与 flex 布局，即 flexbox 下设置 了绝对定位的项目会直接覆盖 flex 原有布局，唯一特例是当绝对定位的 `top/bottom/left/right` 属性值未设定，现以图例说明此特殊项目的位置（当然都已经设置了绝对定位为啥不写具体呢，不存在的……所以只是为了理解绝对定位与 flex 的相互作用）：

    容器宽度 600px，高度 400px，所有项目均有外边距 20px。项目1， 2， 3， 4 按绝对定位确定位置；项目6， 7， 8， 9 按 flex 布局确定位置；项目 5 位置则同时受到绝对定位和 flex 布局影响。

    ![absolute/flex01](https://i.loli.net/2018/06/02/5b1298f9dc72f.png)

    ![absolute/flex02](https://i.loli.net/2018/06/02/5b12991aba644.png)

    ![absolute/flex03](https://i.loli.net/2018/06/02/5b12992b30b7f.png)

    ![absolute/flex04](https://i.loli.net/2018/06/02/5b12994364ebb.png)

    ![absolute/flex05](https://i.loli.net/2018/06/02/5b12993677010.png)

    ![absolute/flex06](https://i.loli.net/2018/06/02/5b1299adea183.png)

    
## 兼容性

PC端及移动端：[CanIUse](https://caniuse.com/#search=flex)

IE10用的是旧版语法，IE11 bug也不少，PC端目前请谨慎使用。移动端建议把旧语法都加上……微信目前已完全支持。

前缀补全请用插件：autoprefixer

在gulp中使用 [gulp-autoprefixer](https://www.npmjs.com/package/gulp-autoprefixer)

还有几个常见的坑：

- 旧版规范中，使用比例伸缩布局时，不同项目的内容长短不同会导致无法均分，这时我们需要给项目设置 `width:0%`

- 不要给项目设置 `margin:auto` 的属性，在部分安卓机下会导致该元素宽度撑开到 100% 占位

- `text-overflow: ellipsis;` 在 `display：flex` 元素上无效

更多兼容性问题可查看：[flexbugs](https://github.com/philipwalton/flexbugs)



参考资料及扩展阅读：[使用 CSS 弹性盒子](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout/Using_CSS_flexible_boxes)
