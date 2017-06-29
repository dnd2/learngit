# CSS
css规范学习

## 指定值、计算值和实际值
制作者定义CSS时，不需要对每个属性都指定一个值，而对于解释网页的用户端（例如：浏览器），当它解析了一个文档并且生成了文档树，它必须为文档树中的每一个元素，根据目标媒介类型所适用的每一个属性，指定一个值。也就是说，无论制作者是否在CSS中定义了某个属性，每个属性都有一个值，可能是制作者定义得值，也可能是属性的初始值，或者浏览器内部样式的值。 

制作者所写的CSS值，可以是一个具体的数字，也可能是一个百分比，或者一个缩放因子，而对于浏览器来说，它必须将用户定义的值转变为可以在屏幕上显示的具体的数值。 

一个属性最终的值的确定需要经过4步计算：首先是通过规则确定一个“指定值”，然后转换成用以继承的“计算值”，如果需要再转换为一个绝对的“使用值”，最后依照局部的环境限定转换为“实际值”。 

1) **指定值**

用户端根据下列规则给每个属性分配指定值（按照优先级排序）： 

	* 如果值中包含层叠，使用层叠。 
	* 否则，如果属性是继承的且元素不是文档的根元素，使用它父元素的计算值。 
	* 否则使用初始值。（初始值在每个属性的定义列表内有说明。）

2) **计算值** 

在层叠中，指定值决定计算值，而指定值可以是一个绝对值（例如颜色值“red”），也可以是一个相对值（例如“em”和“ex”需要根据像素或者绝对长度来计算）。 
对于绝对值，不需要经过计算来得到计算值。 

而相对值必须转换为计算值：百分比要乘以一个参考值（每一个属性都会定义参考值是什么），包含相对单位的值（em，ex，px）必须乘以相应的字体或点的尺寸以得到绝对值，“auto”值必须由各属性给出的公式加以计算，某些关键字（smaller，bolder，inherit）根据它们的定义而加以替换。 

大部分情形下，元素继承的是计算值。不过有一些属性的指定值也可以被继承（例如line-height属性的数字值）。子元素不继承计算值的情况在属性定义中有描述。 
当指定值不是“inherit”时，属性的计算值根据属性定义列表中“计算值”一项来确定。 

3) **使用值**

有时候，计算值可能要到文档被用户端解释的时候才能确定，例如，某个div设定了宽度为其包含块宽度的80%，那么，其计算值需要在用户端确定了其包含块的宽度以后，才能计算出来。 

使用值就是指从绝对值中去除了其他因素以后计算出来的值。

4) **实际值**

原则上，使用值是用户端用来绘制元素的值，但是用户端在某些特定的环境下可能不能使用这个值。例如边框的宽度只能以整数的像素值来显示，因此可能会计算出一个近似的宽度值，再例如，某些显示设备只能用黑白及灰度值来显示彩色的内容。 
实际值就是使用值实际应用中的近似值。

#### 注释
CSS属性的 计算值  (computed value) 由指定的值计算而来:

* 处理特殊的值 [inherit](https://developer.mozilla.org/zh-CN/docs/Web/CSS/inherit) 和 [initial](https://developer.mozilla.org/zh-CN/docs/Web/CSS/initial)
* 根据属性的摘要中关于“计算值”描述的方法计算出值

计算属性的"计算值"通常包括将相对值转换成绝对值(如 em 单位或百分比)。
例如，如一个元素的属性值为 font-size:16px 和 padding-top:2em, 则 padding-top 的计算值为 32px (字体大小的2倍).

然而，有些属性的百分比值会转换成百分比的计算值(这些元素的百分比相对于需要布局后才能知道的值，如 width, margin-right, text-indent, 和 top)。另外，line-height 属性值如是没有单位的数字，则该值就是其计算值。这些计算值中的相对值会在 应用值 确定后转换成绝对值。

计算值的最主要用处是 继承 , 包括 inherit 关键字。

## Supplementary

CSS 中的声明，由 CSS 的特性和值，中间以 ":" 隔开组成。
我们可以使用 CSS 选择器，为选中的元素设置需要的样式。

在介绍 CSS 的特性和值的时候，特地的提到了浏览器应该怎样处理错误的值 -- 应该将包含错误值，不符合句法的值的声明直接忽略。然而有些浏览器却按照自己的方式做了某些纠正，也就是浏览器容错。

那么，有没有想过，在我们给予一个特性正确的值的情况下，浏览器应该怎样处理呢？是否最终你看到的就是你设置的值呢？答案是否定的！
当浏览器解析了一个文档 ( document ) 并且生成了文档树 ( document tree )，那么，它必须为文档树中的每一个元素，根据目标媒介类型所适用的每一个特性，指定一个值。

而开发者给某个元素的 CSS 特性设置某个值，到这个元素的特性值被计算渲染，也就是得到最终的值，需要经过四步计算：

* 通过指定值 ( specified value ) 确定这个值；
* 将这个值分解为一个可以用来继承的值，即计算后的值 ( computed value )；
* 必要情况下把计算后的值转换成一个绝对的值，即使用值 ( used value )
* 根据显示环境的限制，改变使用值以使之能够显示在用户端，最后这个值被称作实际值 ( actual value )。

这些值的计算过程类似工资的计算过程，你的应得工资是一个设置值，实际工资还要减掉你的缺勤，保险，个人所得税等……实际计算出来的数字才是应用到你身上的具体数额。

下面来详细的介绍这四个步骤的四种值。

一、指定值
一般来讲，开发者设定的值，即为指定值，但是最终这个值的确定还需考虑其他几个方面( 按照优先顺序排列 )。

1.**层叠顺序**

首先考虑有层叠顺序的情况，即开发者明确了设置了 CSS 特性的值。
层叠顺序会影响开发者设定的值，所以，应该根据层叠顺序来确定声明的优先级别，优先级高的声明才会起作用。关于层叠顺序，后续会有详细的说明。
例如，开发者在定义样式的时候，加了 '!important' 的声明要优于未加 '!important' 的声明。

~~~css
div{   
    height : 100px;   
    height : 130px !important;   
}  
~~~

其中，带有 '!important' 的 height 声明才是 指定值。
2.继承( inheritance )
如果没有明确的设置这个值，那么会先考虑它是否继承了父元素的值。这时候指定值使用它父元素的值，通常是父元素的计算值。
例如：

~~~html
<p style="color:red;">The greet <em>is</em> good!</p>  
~~~

EM 元素没有指定颜色，他将继承父元素的颜色用来显示字符串 "is"。因此，EM 元素是红色。EM 元素 'color' 特性的指定值在没有层叠影响的情况下，就是 "red"。

二、**初始值**

最后，在不存在以上两种情况的时候，使用元素的初始值。
如果不设置元素的 'width'，它的指定值就是 "auto"；对于 'font-size'，不设置的情况下，其指定值是 "medium"。
关于指定值在 JavaScript 中的获取方法，可以使用 "element.style.property" 语句模式：

~~~html
<p id="container" style="color:red;">The headline <em id="sub">is</em> important!</p>   
<div id="info"></div>   
<script type="text/javascript">   
    window.onload = function() {   
        var container = document.getElementById("container");   
        var sub = document.getElementById("sub");   
        var info = document.getElementById("info");   
        info.innerHTML = "container width :　" + container.style.width  
                +"<br/>container color : " + container.style.color  
                +"<br/>sub color : " + sub.style.color;   
    }   
</script> 
~~~

经过测试可知，此种方式只适合获取设置的值，对于初始值和继承后的值都没有取到。

三、**计算值**

指定值在层叠的过程中被分解成计算值。例如，URI 会被解析成绝对地址，而 'em' 和 'ex' 单位会被计算为 pixel 或者绝对长度。
例如:

~~~html
<div style="width:1em; ">hello!</div>  
~~~

浏览器默认 ‘font-size’ 是 ‘16px’，所以 ‘1em’ 计算值应该是 ‘16px’。
当指定值不是 ‘inherit’ 的时候，计算值应该是 CSS 特性定义的``` "计算后的值"一行所标明的值。例如对 'border-top-width'的特性说明的最后一行：
Computed value: absolute length; '0' if the border style is 'none' or 'hidden'

以上标明，边框宽度的计算值是一个绝对长度，当 border 的设置值是 ‘none’ 或 ‘hidden’ 的时候，计算值为 0。

当 CSS 特性不适合元素时，计算值还是存在的。

~~~html
<div style="width:1em; left:1em;">hello!</div>
~~~

如上代码中，DIV 元素设置 'left' 值为 "1em"，计算后的值为 "16px"；但是，'left' 特性并不适合于非定位元素。

1.**长度值**

长度值适用于水平或垂直方向的尺寸。
长度值表示为 <length>。长度值的格式是： <number> + 单位( e.g., px, em, etc.)，注意，一定要有单位，除非这个值是0。 如果长度值是0，单位可有可无。

可用此类值的 CSS 特性很多，例如，'margin'、'padding'、'height' 和 'width'等。

有些特性支持负的长度值，比如 ‘margin’。但是如果给一个不支持负长度值的特性设置一个负的值，那么这个声明会被忽略。
长度的单位有两种：相对长度和绝对长度。下面对这两类单位详细介绍。

（1）相对长度

相对长度会随着它参考值的变化而变化，不是固定的值。

em : 与 'font-size' 的大小有关，与作用到元素上的 'font-size' 的值大小相等；

ex : 一个小写字母 x 的高度；

px : 像素数( pixels )。

例如：

~~~css
h1 { margin: 0.5 em }      /* em */   
h1 { margin: 1 ex }        /* ex */   
p  { font-size: 12 px }    /* px */  
~~~

（2）绝对长度

in : 英寸 — 等于2.54厘米

cm : 厘米

mm : 毫米

pt : 点 — CSS 2.1里 1pt 等于 1/72 英寸

pc : 皮卡 — 1pc 等于 12pt，也就是 1/6 英寸

例如：

~~~css
h1 { margin: 0.5in }      /* inches  */   
h2 { line-height: 3cm }   /* centimeters */   
h3 { word-spacing: 4mm }  /* millimeters */   
h4 { font-size: 12pt }    /* points */   
h4 { font-size: 1pc }     /* picas */ 
~~~

2.百分比值

百分比值表示为 <percentage>。它的格式是：<number> + %。
常见可用百分比为值的 CSS 特性如：'height'、'width' 等。
百分比值总是跟其他的值有关，比如长度值。

使用值

在处理计算值的过程中，文档没有被格式化，因此，有些值是无法确定的。比如，百分比宽度的元素，最终宽度是与它包含块的宽度有关， 所以，值只有在包含块确定下来之后才能确定。

可以说，使用值是将计算值和有依赖关系的值最终转化成的绝对的值。
利用 JavaScript 来获取元素的使用值，可以采用如下函数：

~~~javascript
function getStyle(obj, style) {   
      var _style = (style == "float") ? "styleFloat" : style;   
      return document.defaultView ? document.defaultView.getComputedStyle(obj, null).getPropertyValue(style) : obj.currentStyle[_style.replace(/-[a-z]/g, function() {   
          return arguments[0].charAt(1).toUpperCase();   
      })];   
}  
~~~

其中，需要注意的是，在 IE 里，浮动的计算值不能直接使用 'float' 特性来取， 需要使用的是 'styleFloat'，可能 IE 中还存在其他类似的情况。请根据实际用途修改函数。

最后，关于使用值，可以直接使用浏览器开发者工具查看，在 Firebug 中，使用值就是 "计算出的样式"。Chrome 里则是 "Computed Style"。

实际值

经过以上三个步骤的处理，使用值基本上成为渲染所需要的值。但是用户端可能不能够在当前环境中使用这个值。例如：

~~~css
div{
    width: 3.1415926px;
}
~~~

在某些浏览器中，只能显示整数类型的长度，因此，虽然上面的宽度在计算后的值与设置的相同，但是，浏览器却没有办法按小数来显示。

Firefox Chrome 等浏览器都会以以一定的方式对值做一些取舍。Firefox 采用了四舍五入的形式，Chrome 中却会直接取整，在这点上需要特别注意哦。

## 继承

每个 CSS 属性定义 的概述都指出了这个属性是默认继承的 ("Inherited: Yes") 还是默认不继承的 ("Inherited: no")。这决定了当你没有为元素的属性指定值时该如何计算值。

1）继承属性

当元素的一个继承属性 （inherited property ）没有指定值时，则取父元素的同属性的计算值 computed value 。只有文档根元素取该属性的概述中给定的初始值（initial value）（这里的意思应该是在该属性本身的定义中的默认值）。

For example:

~~~css
p { color: green }
~~~

HTML:

~~~html
<p>This paragraph has <em>emphasized text</em> in it.</p>
~~~

文本 "emphasized text" 将会呈现为绿色，因为 em 元素继承了 p 元素 color 属性的值，而没有获取color属性的初始值（这个color值用于页面没有指定color时的根元素）。

2) 非继承属性

当元素的一个非继承属性 (在Mozilla code 里有时称之为 reset property  ) 没有指定值时，则取属性的初始值initial value （该值在该属性的概述里被指定）。

As follow:

~~~css
p { border: medium solid }
~~~

HTML

~~~html
<p>This paragraph has <em>emphasized text</em> in it.</p>
~~~
文本 "emphasized text" 没有边框，因为border-style属性 的初始值为 none。

Note:  inherit 关键字 用于显式地指定继承性，可用于继承性/非继承性属性。

[You Don't Know CSS](https://zhuanlan.zhihu.com/p/23829153)

### px, em和rem

px 用来设置字体大小是稳定和精确的
em 是相对于父元素来设定字体尺寸大小的。 [em介绍](https://www.w3cplus.com/css3/define-font-size-with-css3-rem) 
	
	* 浏览器默认的字体尺寸是16px,因此未做调整时1em = 16px，那么12px = 0.75em, 10px = 0.625em，
	* 为了简化计算，通常在body中将字体大小设置为font-size: 62.5%, 这样em的值为 16px * 62.5% = 10px, 12px = 1.2em, 10px = 1em... 即将px除以10得到em的值。 

rem 相对于根元素的字体大小单位。
相关介绍: [web app变革之rem](https://isux.tencent.com/web-app-rem.html)	


### 清除浮动

由于浮动的特性，是元素不在占用原有空间，如果容器没有明确设定高度，则会按照普通元素高度设置，这样会影响其后元素的布局。

1. 采用一个HTML标签，以及css的clear属性，来手工清理浮动 
2. 采用伪类:after，动态建立一个块元素，设定 clear 属性，清理之前的浮动元素
3. 建立 Block Formatting Contexts 来包含浮动元素，而以上两种方法就是清除浮动，跟包含概念还是不同的

    * 如采用overflow ，非 visible 值(overflow:auto/overflow:hidden)
    * 采用display:table/display:table-cell 等table系列属性将父元素变成 table 形式自动包含浮动元素
    * float:left/float:right 方式将父元素同样浮动，就可以包含浮动内容

4. 而在 IE 6/7 的标准文档模式中设置 “width/height/zoom” 等样式触发IE的layout（类似Block Formatting Contexts）来包含浮动元素 

 **Examples** 

 * clear:both 由于定义的清理浮动样式元素所在位置处于浮动元素之下，容器计算后的实际高度就包含了浮动元素。
 * 使用伪元素:after清除 .div:after{display:block;content:'\020';clear:both}  
    
    * after 伪元素是在 CSS 2 规范内提出,IE 6/7 并不支持
    * 在指定该伪元素元素内，所有子元素最后自动生成一个伪元素，并可以为这个伪元素设定样式
    * :after{display:block;clear:both} 通过伪类创建一个块元素，也可用display:table
    * content:'\020', 不使用content:'.'为了节省代码，如使用必须加上visibility:hidden;height:0，将.隐藏，而\020表示空白符的Unicode码 
    * overflow使用
        * overflow 样式值为 非 visilbe 时，实际上是创建了 CSS 2.1 规范定义的 Block Formatting Contexts。创建了它的元素，会重新计算其内部元素位置，从而获得确切高度。这样父容器也就包含了浮动元素高度。这个名词过于晦涩，在 CSS 3 草案中被变更为名词 Root Flow，顾名思义，是创建了一个新的根布局流，这个布局流是独立的，不影响其外部元素的。实际上，这个特性与 早期 IE 的 hasLayout 特性十分相似。
        * 但是当定位子元素部分在父元素外面时，父元素就会对超出其外的子元素进行裁剪，而且CSS3中的box-shadow也会被影藏掉。
        * 注意兼容问题：Block Formatting Contexts 概念是在 CSS 2.1 规范内被提出。因此 IE6/7 中并不被支持，这是由于之前的 IE 版本仅完全实现了 CSS 1 规范标准，以及一部分 CSS 2.0 规范。在 IE 7 中，overflow 值为非 visible 时，可以触发 hasLayout 特性。这同样使得 IE 7 同样可以使容器包含浮动元素。

[浮动元素说明](http://kayosite.com/remove-floating-style-in-detail.html)  
[Block Formatting Context](http://kayosite.com/block-formatting-contexts-in-detail.html)

**overflow**

* overflow:hidden并不隐藏所有溢出元素
    * 拥有overflow:hidden的块元素的父元素不具有position:relative或absolute
    * 内部溢出元素通过postion:absolute定位 
* 根据css2.1规范关于overflow的描述
    * This property specifies whether content of a block container element is clipped when it overflows the element's box. It affects the clipping of all of the element's content except any descendant elements (and their respective content and descendants) whose containing block is the viewport or an ancestor of the element. (此属性(overflow)规定，当一个块元素容器的内容溢出元素的盒模型边界时是否对其进行剪裁。它（此属性）影响被应用元素的所有内容的剪裁。但如果后代元素的包含块是整个视区（通常指浏览器内容可视区域）或者是该容器（定义了overflow的元素）的父级元素时，则不受影响)
    * A descendant box is positioned absolutely, partly outside the box. Such boxes are not always clipped by the overflow property on their ancestors; specifically, they are not clipped by the overflow of any ancestor between themselves and their containing block。(一个绝对定位的后代块元素，部分位于容器之外。这样的元素是否剪裁并不总是取决于定义了overflow属性的祖先容器；尤其是不会被位于他们自身和他们的包含块之间的祖先容器的overflow属性剪裁)

~~~html
    <div class="position">
        <h2>position box</h2>
        <div class="overflow">
            <h3>overflow box</h3>
            <div class="static">
                <p>This is static child element. This is static child element. This is static child element. This is static child element.</p><p>
                </p><p>This is static child element. This is static child element. This is static child element. This is static child element.</p><p>
            </p></div>
            <div class="absolute">This is absolute child element. This is absolute child element. This is absolute child element. This is absolute child element.</div>
        </div>
    </div>
~~~   

~~~css
        h2,h3 {margin:0;padding:0.8em 0;}
        p {margin:0;pading:5px 10px;}
        body {background:#000;color:#fff;}
        .position {margin:100px auto;width:300px;background:#000;border:1px solid #fff;position:relative;#zoom:1;}
        .overflow {width:100%;background:#f00;overflow:hidden;#zoom:1;}
        .static {height:150px;width:450px;background:#00f;}
        .absolute {position:absolute;width:350px;height:80px;top:150px;left:-100px;background:#0c0;}
~~~ 

**CSS样式层叠权重值**

    * 参照CSS2规范 [Calculating a selector's specificity](https://www.w3.org/TR/CSS2/cascade.html#specificity)
    * 选择器权重值的计算
        * A：如果规则是写在标签的style属性中（内联样式），则A=1，否则，A=0. 对于内联样式，由于没有选择器，所以 B、C、D 的值都为 0，即 A=1, B=0, C=0, D=0（简写为 1,0,0,0，下同）
        * B：计算该选择器中ID的数量。（例如，#header 这样的选择器，计算为 0, 1, 0, 0）
        * C：计算该选择器中伪类及其它属性的数量（包括类选择器、属性选择器等，不包括伪元素）。 （例如， .logo[id='site-logo'] 这样的选择器，计算为 0, 0, 2, 0）
        * D：计算该选择器中伪元素及标签的数量。（例如，p:first-letter 这样的选择器，计算为0, 0, 0, 2） 
    * !important
        * !important 用于单独指定某条样式中的单个属性。对于被指定的属性，有 !important 指定的权重值大于所有未用 !important 指定的规则  
    * 关于inherit
        * 继承而来的属性值，权重永远低于明确指定到元素的定义。只有当一个元素的某个属性没有被直接指定时，才会继承父级元素的值

    [原文链接](https://ofcss.com/2011/05/26/css-cascade-specificity.html)   

 **line-height**

 line-height被用来控制行与行之间垂直距离。行高指的是文本行的基线间的距离，但是文本之间的空白距离不仅仅是行高决定的，同时也受字号的影响。

行高指的是文本行的基线间的距离。而基线（Base line），指的是一行字横排时下沿的基础线，基线并不是汉字的下端沿，而是英文字母x的下 端沿，同时还有文字的顶线（Top line）、中线（Middle line）和底线（Bottom line），用以确定文字行的位置
 ![文字的基线](http://files.jb51.net/file_images/article/201408/201408022334032.gif)   

1.1 行高与字体尺寸的差称为行距

![行高与行距](http://files.jb51.net/file_images/article/201408/201408022334033.gif)

* 内容区域: 一行中的每个元素都有一个内容区域，它是由字体尺寸决定的

![内容区域](http://files.jb51.net/file_images/article/201408/201408022334034.gif)

* 行内元素会生成一个行内框（inline box），行内框只是一个概念，它无法显示出来，但是它又确实存在。在没有其他因素影响的时候，行内框等于内容区域，而设定行高则可以增加或者减少行内框的高度，即：将行距的值（行高-字体尺寸）除以2，分别增加到内容区域的上下两边
![行内框与行高](http://files.jb51.net/file_images/article/201408/201408022334035.gif)

* 由于行高可以应用在任何元素上，因此同一行内的若干元素可能有不同的行高和行内框高，例如有如下代码
~~~html
<p style=”line-height:20px;”>行高20px。<strong style=”line-height:50px;”> 行高50px。</strong><span style=”line-height:30px;”>行高30px。</span></p>
~~~

![行内框与行框](http://files.jb51.net/file_images/article/201408/201408022334036.gif)

* 这里又有一个新的概念——行框（line box）。同行内框类似，行框是指本行的一个虚拟的矩形框，其高度等于本行内所有元素中行高最大的值。因此，当有多行内容时，每行都会有自己的行框，
![多行内容的行框](http://files.jb51.net/file_images/article/201408/201408022334037.gif)

注意：行框的高度只同本行内元素的行高有关，而和父元素的高度（height）无关

1.2 行高的计算与继承

以em、ex和百分比为单位的行高，其基数是元素本身的字体尺寸。例如有代码如下：

~~~html
<p style="font-size:20px;line-height:2em;">字高20px，行高2em。</p> <p style="font-size:30px;line-height:2em;">字高30px，行高2em。</p>
~~~

![行高的计算](http://files.jb51.net/file_images/article/201408/201408022334038.gif)

* 行高可以设定得比字体高度小，此时多行的文字将叠加到一起
~~~html
<style>p { font-size : 20px; line-height :10px; }</style>
<p>字高20px，行高10px。此时多行的文字将叠加到一起。</p>
~~~

![比字体高度小的行高](http://files.jb51.net/file_images/article/201408/201408022334039.gif)

* 行高是可继承的，但是继承的是计算值，例如有如下代码：
~~~html
<style>p { font-size :20px; line-height : 2em; }
p span { font-size : 30px; }</style>

<p>字高20px。<span>字高30px。</span></p>
~~~

![行高的不同表现](http://files.jb51.net/file_images/article/201408/2014080223340310.gif)

~~~html
<!--由于继承的是计算值，因此当元素内的文字字体尺寸不一样的时候，如果设定固定的行高很可能造成字体的重叠，例如有如下代码，其显示如图7-26所示。-->

<style>p { font-size : 20px; line-height : 1em; }
p span { font-size : 30px; }</style>

<p>字高20px，行高1em，当文本为多行时可能会发生文字重叠的现象。<span>字高30px。</span></p>
~~~

* 为了避免这种情况，可以为每个元素单独定义行高，但是这样很烦琐，因此可以定义一个没有单位的实数值作为缩放因子来统一控制行高，缩放因子是直接继承的，而不是继承计算值。例如修改上例中的行高为：p { line-height : 1; } 则上例中的XHTML代码显示：
![缩放因子对行高的影响](http://files.jb51.net/file_images/article/201408/2014080223340312.gif)

* 当内容中含有图片的时候，如果图片的高度大于行高，则含有图片行的行框将被撑开到图片的高度

![含有图片的行](http://files.jb51.net/file_images/article/201408/2014080223340313.gif)

注意：图片虽然撑开了行框，但是不会影响行高，因此也不会影响到基于行高来计算的其他属性。
提示：当行内含有图片的时候，图片和文字的垂直对齐方式默认是基线对齐

1.3 浏览器的差别与错误

* 浏览器在显示的时候往往会有自己的表现形式，例如在Opera内，行高将按照CSS定义的将行距除以2增加到内容区域的上下两边，而IE和Firefox则不是完全平分

![不同浏览器对行高的显示](http://files.jb51.net/file_images/article/201408/2014080223340314.gif)

* 相差的1至2个像素在实际显示中一般不会有太大的影响，因此可以忽略不计。比较严重的错误是IE 6.0对于含有图片或者表单元等可替换行内元素的行高失效的问题，不过，在IE 7.0中已经修正了这个错误，但是其表现同其它浏览器也不相同。例如有如下代码

~~~html
<style>#lineHeight4 p { line-height : 60px; }
#lineHeight4 fieldset{ border : 0; }</style>

<div id=”lineHeight4″> 
   <p>内容含有图片在[IE 6]内浏览line-height将失效。<img src=”../../img/ddcat_anim.gif” _fcksavedurl="”../../img/ddcat_anim.gif”" _fcksavedurl="”../../img/ddcat_anim.gif”" alt=”图片” width=”88″ height=”31″ /></p> 
   <form id=”testForm” action=”#”> 
        <fieldset> 
            <p><label for=”test1″>表单元素</label>
            <input type=”text” maxlength=”16″ value=”IE6内行高失效” /></p> </fieldset> 
    </form> 
</div>
~~~

![包含替换元素的行高在IE内失效](http://files.jb51.net/file_images/article/201408/2014080223340315.gif)

IE 7.0中，将半行距分别加在了图片的上下，而由于图片默认是基线对齐，因此文字的基线下移了，这显然不符合CSS中的规定。
对于IE 6.0中行高失效的问题，需要使用CSS Hack手段来针对IE 6.0设定替换元素的上下补白来修正。

[原文](http://www.jb51.net/css/199172.html)


web移动开发
http://www.infoq.com/cn/articles/development-of-the-mobile-web-deep-concept

blog:
https://zhuanlan.zhihu.com/p/26141351
articles:
http://www.cnblogs.com/PeunZhang/archive/2013/01/30/2883464.html
https://segmentfault.com/t/%E7%A7%BB%E5%8A%A8web%E5%BC%80%E5%8F%91/blogs?page=2
框架
http://www.cnblogs.com/fang-beny/p/5234272.html