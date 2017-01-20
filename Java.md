## JavaBean的作用

1. 控制数据的流向，将前台传过来的数据包起来，然后一个一个地插入数据库永久保存
2. 从数据库中用jdbc取出数据，然后包起来，最终传递到前台页面进行公开展览

## Java实例化4种途径

* 使用new操作符
* 调用Class对象的newInstance()方法 (使用反射实现)
* 调用clone()方法，对现有实例的拷贝
* 通过ObjectInputStream的readObject()方法反序列化类

~~~java
// 反射方式创建File对象
try {
	//获得File类的Constructor对象
	Constructor<File> constructor = File.class.getDeclaredConstructor(String.class);
	File file = constructor.newInstance("MyFile.txt");
	file.createNewFile();
catch ( Exception e ) {}	
~~~

### Java中equals和==的区别
java中的数据类型，可分为两类： 
1.基本数据类型，也称原始数据类型。byte,short,char,int,long,float,double,boolean 
  他们之间的比较，应用双等号（==）,比较的是他们的值。 
2.复合数据类型(类) 
  当他们用（==）进行比较的时候，比较的是他们在内存中的存放地址，所以，除非是同一个new出来的对象，他们的比较后的结果为true，否则比较后结果为false。 JAVA当中所有的类都是继承于Object这个基类的，在Object中的基类中定义了一个equals的方法，这个方法的初始行为是比较对象的内存地 址，但在一些类库当中这个方法被覆盖掉了，如String,Integer,Date在这些类当中equals有其自身的实现，而不再是比较类在堆内存中的存放地址了。
  对于复合数据类型之间进行equals比较，在没有覆写equals方法的情况下，他们之间的比较还是基于他们在内存中的存放位置的地址值的，因为Object的equals方法也是用双等号（==）进行比较的，所以比较后的结果跟双等号（==）的结果相同。
 
[Java中equals和==的区别](http://www.cnblogs.com/zhxhdean/archive/2011/03/25/1995431.html)  

**包装类的作用**

* 作为和基本数据类型对应的类类型存在，方便涉及到对象的操作
* 包含每种基本数据类型的相关属性如最大值、最小值等，以及相关的操作方法

**java集合框架**

![Java集合框架的结构图](https://dn-anything-about-doc.qbox.me/document-uid85931labid1094timestamp1436168922546.png?watermark/1/image/aHR0cDovL3N5bC1zdGF0aWMucWluaXVkbi5jb20vaW1nL3dhdGVybWFyay5wbmc=/dissolve/60/gravity/SouthEast/dx/0/dy/10)

**Collection和Collections的区别**

* java.util.Collection 是一个集合接口 提供了对集合对象进行基本操作的通用接口方法。Collection接口的意义是为各种具体的集合提供了最大化的统一操作方式
* java.util.Collections 是一个包装类 包含有各种有关集合操作的静态多态方法。此类不能实例化，就像一个工具类，服务于Java的Collection框架。

[java中Collection与Collections的区别](https://my.oschina.net/leoson/blog/131905)

**ArrayList和Vector的区别**

1. 同步性:Vector是线程安全的，也就是说是同步的，而ArrayList是线程序不安全的，不是同步的 
2. 数据增长:当需要增长时,Vector默认增长为原来一倍，而ArrayList却是原来的一半 

**HashMap和Hashtable的区别**

1. 历史原因:Hashtable是基于陈旧的Dictionary类的，HashMap是Java 1.2引进的Map接口的一个实现
2. 同步性:Hashtable是线程安全的，也就是说是同步的，而HashMap是线程序不安全的，不是同步的 
3. 值：只有HashMap可以让你将空值作为一个表的条目的key或value


[JAVA中的Random()函数
](http://www.cnblogs.com/ningvsban/p/3590722.html)

**Java String和StringBuilder、StringBuffer浅谈**
[String,StringBuilder,StrinbBuffer](http://www.jianshu.com/p/853f8b006edd)

[Java读写大文本文件](http://www.cnblogs.com/duanxz/p/4874712.html)