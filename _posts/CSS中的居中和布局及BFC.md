/**
title: CSS中的居中和布局及BFC
date: 2018-06-15
tags: CSS,布局,BFC,居中
*/


### CSS 水平居中
#### 行内元素
对父元素设置`text-align:center;`即可。
```css
.parent{
    text-align: center;
}
```

#### 单个块元素
块元素本身就占据整行，如果对其进行水平居中设置，说明它的宽度小于父级容器的宽度。这时只要设置块元素水平方向上的margin属性为auto即可。
```css
div{
    margin: auto; /*  */
}
```

#### 多个块元素
多个块元素居中有两种方式：
1. 设置块元素`display： inline-block;`，将其转换成行内块级元素，这时候父级元素设置`text-align:center;`就可以对其生效使之居中。
2. 使用`flexbox`布局；设置父级元素(.parent)`display:flex`
```css
.parent{
    display: flex;
    justify-content:center;
}
```
3. 父元素float:left;left:50%; 子元素left:-50%;
```css
.parent {
    list-style: none;
    float: left;
    position: relative;
    left: 50%;  /*父元素 偏移 50% 后，左边线就移到了中线位置*/
    padding: 0;
}

.parent div {
    margin-right: 8px;
    float: left;
    position: relative;
    left: -50%; /* 子元素再向左方向偏移一半 */
}
```
父元素设置float是为了不让它的宽度等于100%，而是等于子元素宽度之和。（设置float后，块元素就不再是块元素，它脱离正常的文档流）
然后设置相对位置（position:relative），使其左偏移50%，这样它的左边线在中心位置。在这种情况下，只要向左移动它自身宽度的50%就可以达到居中位置。
想要再向左偏移`WIDTH * 50% `，设置其子元素的位置left:-50%即可。

### CSS垂直居中

#### 单行的行内元素
已知父元素高度的情况下，设置元素的`line-height`等于父元素高度。
```css
.parent{ /*这是父元素的样式设置*/
    height: 60px;
    background: green;
    color: white;
}
span{
    line-height: 60px; /* 行内元素的行高设置为跟父元素高度一直 */
}
```

#### 多行的行内元素
设置父元素的为`table-cell`
```css
.parent{
    display: table-cell;
    vertical-align: middle;
    height: 80px; /* 父元素 不受高度限制，可任意调整 */
}
```

#### 已知高度的块元素
```css
div{
    height: 100px;
    position: relative;
    top: 50%;
    margin-top: -50px;  /* margin-top值为自身高度(此处height=100px)的一半 */
}
```

### 块元素垂直水平居中

#### margin：auto + absolute 
针对块级元素，支持IE8+，不受元素的宽高影响，可以任意更改元素的宽高，而其始终会自适应显示。
```css
div{
    width: 200px;
    height: 200px;

    /*下面的是关键设置*/
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
    /*上面的是关键设置*/
}
```
原理呢，据说用三个相关的文档标准才能解释清楚。
下面的是尝试性解释：

1. 因为 `margin:auto` 默认只会计算左右边距; 上下方向如果设置为auto时，默认值为0；
2. 但对于绝对定位的元素，`margin-top:auto`和`margin-bottom:auto` 的值就不一定是0了，而是通过计算所得；
3. 因为设置了`top:0; bottom:0; right:0; left:0;`，所以子元素会填满父元素的空间；
4. `margin:auto;`在第三步的基础上进行计算，就会让元素垂直水平居中

【参考文章】

[Absolute Horizontal And Vertical Centering In CSS](https://www.smashingmagazine.com/2013/08/absolute-horizontal-vertical-centering-css/)

[position:absolute和margin：auto 连用实现元素水平垂直居中](https://www.cnblogs.com/ada-blog/p/7242664.html)

#### margin 负间距
或许是目前最流行的方式，因为道理很好理解。它的原理跟元素水平居中的第三种方案类似。缺点是不能随意改变宽高，因为`left`和`margin-left`都是在元素本身设置。而水平居中的方案之所以可以使用 **百分比**，主要是借助了父元素。
```css
div{
    width:200px;
    height: 200px;
    position: absolute;
    left:50%;
    top:50%;
    margin-left:-100px;
    margin-top:-100px;
}
```

#### transform
IE8不支持， 利用CSS3的特性
```css
div{
    width: 200px;
    height: 200px;
    background: green;
    position:absolute;
    left:50%;    /* 定位父级的50% */
    top:50%;
    transform: translate(-50%,-50%); /*自身宽高的50% */
}
```

#### Flex方式
```css
.container{
     height:600px;
     /*敲黑板，下面三行代码是关键*/
     display:flex;
     justify-content:center;
     align-items:center; /* 只要三句话就可以实现不定宽高水平垂直居中。 */
}
.container > div{
    background: green;
}
```

#### display:table-cell 
```css
.box {
    background-color: #FF8C00;
    width: 300px;
    height: 300px;
    display: table;
}
.content {
    background-color: #F00;
    display: table-cell;
    vertical-align: middle;
    text-align: center;
}
.inner { /* 这个才是需要垂直水平居中的元素 */
    background-color: #000;
    display: inline-block;
    width: 20%;
    height: 20%;
}
```


### CSS 盒模型
CSS有两种盒模型： **标准盒模型** 和 **IE盒模型**

盒模型中的几个概念： **内容(content)**、**内边距(padding)**、**边框(border)**和 **外边距(margin)**。

再说下另一个概念： **元素宽高**。

*上述两种模型的区别在于**元素宽高**的定义*

标准盒模型：width = content;
IE盒模型：width = content + padding + border; 


*用CSS如何设置两种盒模型？*

答案： 设置`box-sizing`属性值。 content-box：标准盒模型； border-box：IE盒模型

```css
/* 设置当前盒子为 标准盒模型（默认） */
box-sizing: content-box;
/* 设置当前盒子为 IE盒模型 */
box-sizing: border-box;
```

*JS如何获取元素的盒模型的宽高？*
1. `element.style.width/height;`
缺点：通过这种方式，只能获取内联样式，不能获取内嵌的样式和外链的样式。
2. `element.currentStyle.width/height;` 弥补了`element.style.width`的不足，但只IE特有
3. `window.getComputedStyle(element).width/height;` 通用型，跟`currentStyle`作用一样，但可以支持Chrome、FireFox等
4. `element.getBoundingClientRect().width/height;`
此 api 的作用是：获取一个元素的绝对位置。绝对位置是视窗 viewport 左上角的绝对位置；可以拿到四个属性：left、top、width、height。


[深入理解CSS盒模型](https://www.cnblogs.com/chengzp/p/cssbox.html)

[CSS盒模型的深度思考及BFC](https://www.cnblogs.com/smyhvae/p/8512617.html)



#### BFC
**Box** 是 CSS布局中的基本单位。
简单来说，若干个Box(盒模型)组成了一个页面。在HTML中，每个元素都有自己默认的Box类型。但是我们也可手动更改它们的Box类型。不同类型的Box，有不同的渲染规则（比如它们的子元素如何定位，以及子元素的之间的相互作用关系等等)。

**Box类型:**

**block-level box**：display 属性为 block, list-item, table 的元素，会生成 block-level box。并且参与 block fomatting context；
**inline-level box**：display 属性为 inline, inline-block, inline-table 的元素，会生成 inline-level box。并且参与 inline formatting context；
**run-in box**： css3 中才有， 这儿先不讲了

**Formatting context**
Formatting context 是 W3C CSS2.1 规范中的一个概念。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。最常见的 Formatting context 有 Block fomatting context (简称BFC)和 Inline formatting context (简称IFC)。

CSS2.1 中只有 BFC 和 IFC, CSS3 中还增加了 GFC 和 FFC。

**BFC 定义**
BFC(Block formatting context)直译为"块级格式化上下文"。它是一个独立的渲染区域，只有Block-level box参与， 它规定了内部的Block-level Box如何布局，并且与这个区域外部毫不相干。外部元素不会影响里面的元素，里面的元素也不会影响外面的元素。

**BFC布局规则：**
1. 内部的Box会在垂直方向，一个接一个地放置；
2. Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠；
3. 每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此；
4. BFC的区域不会与float box重叠；
5. BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素；反之也如此；
6. 计算BFC的高度时，浮动元素也参与计算；

**会形成BFC的情况**
1. 根元素
2. float属性不为none
3. position为absolute或fixed
4. display为inline-block, table-cell, table-caption, flex, inline-flex
5. overflow不为visible

[前端精选文摘：BFC 神奇背后的原理](http://www.cnblogs.com/lhb25/p/inside-block-formatting-ontext.html)


### CSS 布局

#### 使用position属性布局
定位技术(position)允许我们将一个元素从它在页面的原始位置准确地移动到另外一个位置。
`position`属性的默认值是`static`，对元素设置其它的任何值，都会认为这个元素是`positioned`的元素。
- 静态定位(Static positioning)是每个元素默认的属性——它表示“将元素放在文档布局流的默认位置——没有什么特殊的地方”。
- 相对定位(Relative positioning)允许我们相对元素在正常的文档流中的位置移动它——包括将两个元素叠放在页面上。这对于微调和精准设计(design pinpointing)非常有用。
- 绝对定位(Absolute positioning)将元素完全从页面的正常布局流中移出，类似将它单独放在一个图层中. 我们可以将元素相对于页面的 `<html>` 元素边缘固定，或者相对于离元素最近的被定位的祖先元素(ancestor element)。绝对定位在创建复杂布局效果时非常有用，例如通过标签显示和隐藏的内容面板或者通过按钮控制滑动到屏幕中的信息面板.
- 固定定位(Fixed positioning)与绝对定位非常类似，除了它是将一个元素相对浏览器视口固定，而不是相对另外一个元素。 在创建类似页面滚动总是处于页面上方的导航菜单时非常有用。

#### 使用float属性布局
`float`属性是布局中的另一个控制因子。
float 可以实现文字环绕功能。

下面的例子是利用`float`实现的两栏布局
```css
.div1{
    float: left;
    width: 49%;
}

.div2{
    float: right;
    width: 49%;
}
```

`clear`用于控制`float`属性；
用 left 元素左侧不允许出现浮动元素
用 right 元素右侧不允许出现浮动元素
用 both 元素左侧右侧均不允许出现浮动元素

浮动元素会导致父级元素的塌陷，清除浮动导致的塌陷可以使用如下方式， 
```css
.clearfix {
  overflow: auto;
  zoom: 1; /* 支持IE6 需要这一行*/
}
```

关于float属性，有一条需要明确的就是：**浮动的块虽然脱离的正常的文档流，但是还会占有正常文档流的文本空间** 关于这句话的理解，请看下面示例
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>float</title>
    <style>
        div {
            height: 100px;
            border: 1px solid #ccc;
        }
        .div1 {
            float: left;
            width: 200px;
            border: 3px dashed green;
        }
        .div2 {
            border: 5px solid red;
        }
    </style>
</head>

<body>
    <div class="div1">This is div1</div>
    <div class="div2">
        <p>
            This is div2<br>
            <strong style="color:red;">根据边框，我们可以看到“div2” 跟“div1”有重叠部分， div2占据了<br>原先div1（为设置float属性前）的空间。但是我们发现div2中<br>的文字并没有占据div1的空间，而是紧挨着div1</strong>
        </p>
    </div>
</body>
</html>
```

#### 媒体查询
使用媒体查询可以在不同的设备上以一种比较友好的方式显示内容。这是实现响应式设计的关键特性。

#### Flex
弹性盒子，CSS3中比较新的特性，存在着兼容性问题，但是在移动端的支持还可以。


#### 其它一些布局方式
**百分比布局**、**inline-block**、**Grid网格布局**

[学习CSS布局](http://learnlayout.com/)