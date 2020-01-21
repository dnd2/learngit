### 配置国内镜像

需要为此设置两个环境变量：PUB_HOSTED_URL和FLUTTER_STORAGE_BASE_URL，然后再运行 Flutter 命令行工具。

`上海交通大学 Linux 用户组
FLUTTER_STORAGE_BASE_URL: https://mirrors.sjtug.sjtu.edu.cn
PUB_HOSTED_URL: https://dart-pub.mirrors.sjtug.sjtu.edu.cn

Flutter 社区
FLUTTER_STORAGE_BASE_URL: https://storage.flutter-io.cn
PUB_HOSTED_URL: https://pub.flutter-io.cn`

### Color Class

An immutable 32 bit color value in ARGB format.

Consider the light teal of the Flutter logo. It is fully opaque, with a red channel value of 0x42 (66), a green channel value of 0xA5 (165), and a blue channel value of 0xF5 (245). In the common "hash syntax" for colour values, it would be described as #42A5F5.

Here are some ways it could be constructed:

~~~javascript
    Color c = const Color(0xFF42A5F5);
    Color c = const Color.fromARGB(0xFF, 0x42, 0xA5, 0xF5);
    Color c = const Color.fromARGB(255, 66, 165, 245);
    Color c = const Color.fromRGBO(66, 165, 245, 1.0);
~~~

Note: 

If you are having a problem with Color wherein it seems your color is just not painting, check to make sure you are specifying the full 8 hexadecimal digits. If you only specify six, then the leading two digits are assumed to be zero, which means fully-transparent:

~~~javascript
    Color c1 = const Color(0xFFFFFF); // fully transparent white (invisible)
    Color c2 = const Color(0xFFFFFFFF); // fully opaque white (visible)
~~~

New Words:
    * immutable [ɪ'mjutəb(ə)l] adj. 不可改变的；永恒的
    * teal [til] n. 蓝绿色
    * wherein [weər'ɪn] adv. 其中；在那种情况下

### 子元素超出父元素宽高是否会报错？ 

    * [原文](https://segmentfault.com/a/1190000015407800)
    * [Related Article](http://rang.jx.cn/mobile/flutter-text-widgets/)

    - 如果父元素是Container，那么子元素超出父元素就不会报错
    - 如果子元素被Column或Row包裹，那么子元素超出父元素就会报错
    
### Flutter svg支持


    