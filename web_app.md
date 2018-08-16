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


### 移动Web适配

* 移动适配: 同一套代码在不同分辨率的手机上跑时，页面元素间的间距，留白，以及图片大小会随着变化，在比例上跟设计稿一致。
    1. 通过rem适配方式
        * 设置viewport的宽度与设备宽度一致，width=device-width, 这样1个CSS像素与物理像素尺寸一致
        * 长度单位rem是相对于html标签的font-size来计算的。例如html标签设置font-size:36px，同时div设置width:1.2rem。那么这个div的宽度就是1.2rem=36px*1.2=43.2px
        * font-size是根据dpr来计算



### 常见问题

1. ios点击输入框，界面放大解决方案
    - <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
    - ios的select、input、样式显示的跟安卓不一样，可以用-webkit-appearance:none



###  Mui, HTML5+ FAQ

1. 自动弹出软键盘
    - autofocus不一定在所有Android平台支持自动弹出软键盘，可以通过native.js来强制弹出：

    ~~~html
       <!DOCTYPE html>
        <html>
            <head>
                <meta charset="utf-8">
                <title>Native.js</title>
                <script type="text/javascript">
        // H5 plus事件处理
        function plusReady(){
            var Context = plus.android.importClass("android.content.Context");
            var InputMethodManager = plus.android.importClass("android.view.inputmethod.InputMethodManager");
            var main = plus.android.runtimeMainActivity();
            var imm = main.getSystemService(Context.INPUT_METHOD_SERVICE);
            imm.toggleSoftInput(0,InputMethodManager.SHOW_FORCED);
        }
        document.addEventListener("plusready",plusReady,false);
                </script>
            </head>
            <body>
                <button onclick="plus.webview.currentWebview().close()">Close</button><br/>
                <input type="text" autofocus="autofocus"/>
                <br/>
                打开页面后编辑框自动获取焦点并显示软键盘
            </body>
        </html> 
    ~~~
    - 注意：autofocus属性只有4.0以上版本才支持
    - iOS打开页面自动弹出键盘(input不要添加autofocus)

    ~~~html
        <!DOCTYPE html>
        <html>
            <head>
                <meta charset="utf-8">
                <title>Native.js</title>
                <script type="text/javascript">
        // H5 plus事件处理
        function plusReady(){

            var webView = plus.webview.currentWebview().nativeInstanceObject();
            webView.plusCallMethod({"setKeyboardDisplayRequiresUserAction":false});
            document.getElementById("testautofocus").focus();
        }
        document.addEventListener("plusready",plusReady,false);
                </script>
            </head>
            <body>
                <button onclick="plus.webview.currentWebview().close()">Close</button><br/>
                <input type="text" id="testautofocus"/>
                <br/>
                打开页面后编辑框自动获取焦点并显示软键盘
            </body>
        </html>
    ~~~

    - 比较全面的解决了打开软键盘和autofocus的问题

    ~~~js
        var openSoftKeyboard = function() {
        if(mui.os.ios){
            var webView = plus.webview.currentWebview().nativeInstanceObject();
            webView.plusCallMethod({
                "setKeyboardDisplayRequiresUserAction": false
            });
        }else{
            var webview = plus.android.currentWebview();
            plus.android.importClass(webview);
            webview.requestFocus();
            var Context = plus.android.importClass("android.content.Context");
            var InputMethodManager = plus.android.importClass("android.view.inputmethod.InputMethodManager");
            var main = plus.android.runtimeMainActivity();
            var imm = main.getSystemService(Context.INPUT_METHOD_SERVICE);
            imm.toggleSoftInput(0, InputMethodManager.SHOW_FORCED);
        }
    }

    /*页面隐藏事件*/
            plus.webview.currentWebview().addEventListener("hide",function(e){
                document.getElementById("search-text").value="";
                    document.getElementById("search-text").blur();/*搜索框取消焦点，关闭软键盘*/
            });
            /* 页面显示事件 */
            plus.webview.currentWebview().addEventListener("show",function(e){
                setTimeout(function() {/*自动打开软键盘，搜索框获取焦点*/
                    openSoftKeyboard();
                    document.getElementById("search-text").focus();
                }, 300);
            });
    ~~~

    - Demo 3

    ~~~js
        var nativeWebview, imm, InputMethodManager;
        var initNativeObjects = function() {
            if (mui.os.android) {
                var main = plus.android.runtimeMainActivity();
                var Context = plus.android.importClass("android.content.Context");
                InputMethodManager = plus.android.importClass("android.view.inputmethod.InputMethodManager");
                imm = main.getSystemService(Context.INPUT_METHOD_SERVICE);
            } else {
                nativeWebview = plus.webview.currentWebview().nativeInstanceObject();
            }
        };
        var showSoftInput = function() {
        if (mui.os.android) {
            imm.toggleSoftInput(0, InputMethodManager.SHOW_FORCED);
        } else {
            nativeWebview.plusCallMethod({
                "setKeyboardDisplayRequiresUserAction": false
            });
        }
        setTimeout(function() {
            var inputElem = document.querySelector('input');
            inputElem.focus();
            /*第一个是search，加上激活样式*/
            inputElem.parentNode.classList.add('mui-active'); 
            }, 200);
        };
        mui.plusReady(function() {
            setTimeout(function() {
                initNativeObjects();
                showSoftInput();
            },50);
        });
    ~~~

2. 深入理解高度。获取屏幕、webview、软键盘高度
    - [链接](http://ask.dcloud.net.cn/article/205)
3. [使用MUI 软键盘弹起挤压页面](https://blog.csdn.net/kk_yanwu/article/details/73332704)