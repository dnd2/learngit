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

    