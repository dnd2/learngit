## Web移动开发笔记

### meta viewport中target-densitydpi属性

一个屏幕像素密度是由屏幕分辨率决定的，通常定义为每英寸点的数量（dpi）。Android支持三种屏幕像素密度：低像素密度，中像素密度，高像素密度。一个低像素密度的屏幕每英寸上的像素点更少，而一个高像素密度的屏幕每英寸上的像素点更多。Android Browser和WebView默认屏幕为中像素密度。

* 下面是 target-densitydpi 属性的 取值范围
    - device-dpi –使用设备原本的 dpi 作为目标 dp。 不会发生默认缩放。
    - high-dpi – 使用hdpi 作为目标 dpi。 中等像素密度和低像素密度设备相应缩小。
    - medium-dpi – 使用mdpi作为目标 dpi。 高像素密度设备相应放大， 像素密度设备相应缩小。 这是默认的target density.
    - low-dpi -使用mdpi作为目标 dpi。中等像素密度和高像素密度设备相应放大。
    - <value> – 指定一个具体的dpi 值作为target dpi. 这个值的范围必须在70–400之间。

 [原地址](http://blog.csdn.net/fengri5566/article/details/9414599)   

 ### 设备像素比

 window.devicePixelRatio是设备上物理像素和设备独立像素(device-independent pixels(dips/dp))的比例。公式：window.devicePixelRatio = 物理像素/dips

 设备对立像素与屏幕密度有关。dip可用来辅助区分视网膜设备还是非视网膜设备。

 所有非视网膜屏幕的iphone在垂直的时候，宽度为320物理像素。当你使用<meta name="viewport" content="width=device-width">的时候，会设置视窗布局宽度（不同于视觉区域宽度，不放大显示情况下，两者大小一致，见下图）为320px, 于是，页面很自然地覆盖在屏幕上。这样，非视网膜屏幕的iphone上，屏幕物理像素320像素，独立像素也是320像素，因此，window.devicePixelRatio等于1。而视网膜屏，纵向显示时屏幕物理像素为640，当设置了viewport，其视区宽度不是640而是320，这是为了更好的阅读体验——更适合文字大小。此时的window.devicePixelRatio为2。

 1. PPI(Pixel Per Inch) 屏幕像素密度  是衡量单位物理面积内拥有像素值的情况。

 ![屏幕像素密度](https://res.infoq.com/articles/development-of-the-mobile-web-deep-concept/zh/resources/original.png)

 * 像素密度的实际意义是什么？
    - PPI值越高意味着在同一实际尺寸的物理屏幕上容纳更多的像素，展示更多画面细节，意味着更平滑的画面。
 * 它表达的是什么？ 
    - 它代表的是物理像素
 * 或高或低对设备显示来说有什么影响？
 * PPI 计算

    ![PPI计算公式](https://res.infoq.com/articles/development-of-the-mobile-web-deep-concept/zh/resources/samsung.jpg)   


2. 什么是Pixel

* 设备像素

    无论是早期的CRT显示器还是如今的LCD显示器，都是基于点阵的。即由一些列的小点排列成一个大的矩形，不同的小点通过显示不同的颜色来显示成图像。

    ![6x6个小点排列成的矩阵](https://res.infoq.com/articles/development-of-the-mobile-web-deep-concept/zh/resources/Dots5.png)

    注意每一个像素（pixel，也可以称之为dot）又是由三个子像素(subpixel)红绿蓝组合而成。当需要显示图片信息时，它的工作原理可以如下图所示：

    ![像素成像示例](https://res.infoq.com/articles/development-of-the-mobile-web-deep-concept/zh/resources/pixel_zoom_in.jpg)

    上图中的左侧是放大之后我们能看到的像素，而右侧就是对应像素在显示器上的显示情况了

    注意上图代表的仅是LCD显示器的物理像素情况，早期的CRT显示器的物理像素同样也是由独立的点组成。但是不存在subpixel的概念，情况如下图所示：

    ![CRT显像](https://res.infoq.com/articles/development-of-the-mobile-web-deep-concept/zh/resources/xxx1.jpg)

    上面描述的这些显示器上的像素我们就称之为物理像素(physical pixel)或者设备像素(device pixel)。

* CSS像素 

    用于控制元素样式的样式单位像素。它是一个相对值。