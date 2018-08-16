###相关资料

* Android Training
	* [Base Knowledges](http://hukai.me/blog/categories/android-training/) 
	* [Offical Tutorial](http://hukai.me/android-training-course-in-chinese/index.html)

* Language Points
	* [知识梳理](https://juejin.im/post/587dbaf9570c3522010e400e) 

* Animations
	* [Android animation](http://androidblog.cn/index.php/Source/index/p/1)
* code snippet
	* [Common code](https://github.com/Blankj/AndroidUtilCode/blob/master/README-CN.md)

* Project Cases
	* http://www.jianshu.com/p/61efdc826c01

* Framework
	* [动画框架](https://github.com/lgvalle/Material-Animations) 

* Blog
	* [stormzhang](http://stormzhang.com/posts/)
	* [JackPeng](http://yuanfentiank789.github.io/) 
	* [androidhive](http://www.androidhive.info/)

* Others
	* [打造无敌解耦的BaseActivity](https://luhaoaimama1.github.io/2016/03/23/BaseActivity/)  
	* [Android开发相关的博客、文章、教程](https://github.com/HanderWei/Android-Blogs) 

**.bash_profile 生效**

exec bash --login

**java.lang.UnsupportedClassVersionError: com/android/build/gradle/AppPlugin : Unsupported major.minor version 52.0**

The issue is because of Java version mismatch. You get this error because a Java 7 VM tries to load a class compiled for Java 8. Java 8 has the class file version 52.0 but a Java 7 VM can only load class files up to version 51.0.

gradle命令的执行环境是在gradle.properties配置的，其指向改为：

org.gradle.java.home=/Applications/Android Studio.app/Contents/jre/jdk/Contents/Home

**设置JAVA_HOME和ANDROID_HOME**

* 配置JAVA_HOME环境变量

	~~~text
		# 使用vim打开.bash_profile文件，加入java环境变量
		$ vim .bash_profile
		export JAVA_HOME=$(/usr/libexec/java_home)
	~~~		  
	
* 配置ANDROID_HOME环境变量
	
	~~~text
		$ vim .bash_profile 
		export ANDROID_HOME=/Applications/ADT/sdk
		export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools
	~~~	
* 检查是否生效
	
	~~~text
		$ source .bash_profile
		$ echo $JAVA_HOME 
		$ java -version
		
		$ source .bash_profile
		$ echo $ANDROID_HOME 
		$ adb
	~~~
	
* 配置GRADLE_HOME
	* 下载Gradle并解压缩到任意路径如：	/Applications/gradle-2.3
	* 配置环境变量
		
	  ~~~text
	  	$ vim .bash_profile 
		export GRADLE_HOME=/Applications/gradle-2.3;
		export PATH=$PATH:$GRADLE_HOME/bin
		
		// 检查是否生效
		$ source .bash_profile
		$ echo $GRADLE_HOME 
		
		或者：
		$ gradle -version
	  ~~~	
	 
* 配置MAVEN_HOME
	* 使用brew下载并配置 	 $ brew install maven
	* 正常配置Maven
	
	~~~text
		将下载的maven解压并复制/移动到你需要的目录 
		比如：/usr/local/maven
		
		$ vim .bash_profile
		export MAVEN_HOME=/usr/local/maven/apache-maven-x.x.x
		export PATH=$MAVEN_HOME/bin:$PATH
		是否生效
		$ source .bash_profile
		$ echo $MAVEN_HOME
		$ mvn -version
	~~~
	* 重新设置本地Repository的位置
	
	~~~text
		# 在maven安装目录的conf目录下：
		$ vi settings.xml
		添加：<localRepository>具体的绝对路径</localRepository>
	~~~
	
	* 配置代理  修改~/.m2文件夹下的settings.xml文件，如果没有则去MAVEN_HOME/conf目录下复制过来
	
	~~~text
		<settings>    
		...    
		  <proxies>    
		    <proxy>    
		      <active>true</active>    
		      <protocol>http</protocol>    
		      <host>ip</host>    
		      <port>port</port>    
		    </proxy>    
		  </proxies>    
		...    
		</settings> 
	~~~
	
	
 ##Android Install on Device Failure [INSTALL_CANCELED_BY_USER]
 
 Happens to my Xiaomi phone after updated it to MIUI 8. Took me hours to figure it out!

Check the followings if you're a victim too:

* Go to Settings -> Permissions -> Install via USB: Uncheck your App if it's listed.
* Go to Settings -> Additional Settings -> Privacy: Check the Unknown Sources option.
* Finally go to Settings -> Additional Settings -> Developer options: Check the Install via USB option.

P.S. don't update MIUI unless necessary!

**显示手机端口号**	

~~~text
adb devices
// 如果不能，要记得重启adb
sudo service udev restart
sudo adb kill-server
sudo adb start-server
~~~

**Android的大组件**

* Acitvity
* Service
* Boradcast Receiver
* Content Provider

**Android Studio项目目录结构**

* app 存放代码、资源
* gradle (是Google推荐使用的一套基于Groovy的编译系统脚本) [官方文档](https://developer.android.com/studio/build/index.html) 
  1. gradle目录下包含gradle wrapper的配置文件
  2. Android Studio 默认未启用gradle wrapper, 可通过File -> Settings -> Build,Excution,Development -> Gradle 配置
* build.gradle 项目全局的gradle构建脚本
* gradel.properties gradle全局配置文件,里面所配置的属性会影响项目所有的gradle编译脚本
* grdlew & gradlew.bat 用于在命令行执行gradle命令
* local.properties 用于指定本机中的Andorid SDK路径，通常自动生成
* settings.gradle 用于指定项目中引入的模块
* proguard-rules.pro 指定项目代码的混淆规则


**AppCompatActivity类**

向下继承Acitivty,可将Acitivity在各个系统中增加的特性和功能最低兼容到Android2.1


**项目资源**

* res/layout
	* drawable 开头的文件用于存放图片
	* minmap 开头文件用于存放应用图标



**可选布局文件**

使用XML定义界面布局而不是动态生成的好处:
* 可以为不同大小的屏幕创建不同的布局文件 

## LinearLayout

A Layout that arranges its children in a single column or a single row. <font color="#039BE5">LinearLayout</font> is a view group (a subclass of ViewGroup) that lays out child views in either a vertical or horizontal orientation, as specified by the android:orientation attribute. Each child of a LinearLayout appears on the screen in the order in which it appears in the XML.

Two other attributes, <font color="#039BE5">android:layout_width</font> and <font color="#039BE5">android:layout_height</font>, are required for all views in order to specify their size.

Because the LinearLayout is the root view in the layout, it should fill the entire screen area that's available to the app by setting the width and height to "match_parent". This value declares that the view should expand its width or height to match the width or height of the parent view.

<font color="#039BE5">**android:id**</font> 

* 视图唯一标识，可在程序中通过该标识引用对象 (a unique identifier for the view)
* 当需要从XML引用资源对象，必须使用@符号，@之后是资源类型(此处是id)，然后是/后跟资源名字 `android:id="@+id/edit_message"`
* + 号只在第一次定义一个资源 ID 的时候需要。它告诉 SDK——此资源 ID 需要被创建。在应用程序被编译之后，SDK 就可以直接使用这个 ID
* edit_message 是在项目文件 gen/R.java 中创建一个新的标识符，这个标识符和 EditText 关联。

<font color="#039BE5">**android:layout_width & android:layout_height**</font>

* 不建议使用指定宽高
* 使用wrap_content, 这样可以保证视图只占据内容大小的空间,而match_parent会占满整个屏幕，因为它适应父布局大小

<font color="#039BE5">**android:hint**</font>

* This is a default string to display when the text field is empty.
* The "@string/edit_message" value refers to a string resource defined in a separate file.

<font color="#039BE5">**android:layout_weight**</font>

* 所有的 View 默认的权重是 0
* 权重的值指的是每个部件所占剩余空间的大小, 该值与同级部件所占空间大小有关
* 使用权重的前提一般是给 View 的宽或者高的大小设置为 0dp

~~~text

	<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <EditText
        android:id="@+id/input_message"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:hint="Type something"/>

    <Button
        android:id="@+id/button1"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:text="Send"/>
	</LinearLayout>
~~~

Note: 

* 当orientation设置为horizontal时才能将layout_width设置为0dp的方式
* 因为设置了layout_weight，所以不应该由layout_width来决定其宽度
* 将EditText和Button的layout_weight都设置为1表示2个控件水平方向平分宽度
* 系统会将layout_weight值相加之和，每个控件所占大小比例由控件的layout_weight值除以这个总值得到 （因此想让EditText占屏幕宽度3/5，Button占屏幕2/5，EditText设置为3，Button设置为2）

~~~text

	<EditText
        android:id="@+id/input_message"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:hint="Type something"/>

    <Button
        android:id="@+id/button1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Send"/>
~~~

Note: 这样EditText会占用除Button自身宽度外的剩余空间

###Intent

是各组件进行交互的一种重要方式，可用于启动Activity、启动服务及发送广播等场景。

1 显示intent

~~~java
button.setOnClickListener(new View.onClickListener() {
	@Override
	public void onClick(View v) {
		// 第一个参数指定活动的上下文，第二参数指定要启动的目标活动
		Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
		startActivity(intent);
	}
});
~~~

2 隐式intent

通过在AndoridMainifest.xml中指定当前活动的action和category来响应活动

	<activity android:name=".SecondActivity">
		<intent-filter>
			<action android:name="com.example.activitytest.ACTION_START" />
			<category android:name="android.intent.category.DEFAULT" />
		</intent-filter>
	</activity>

~~~java
button.setOnClickListener(new View.OnClickListener() {
	@Override
	public void onClick(View v) {
		Intent intent = new Intent("com.example.activitytest.ACTION_START");
		startActivity(intent);
	}
});
~~~

Note: 这里由于category指定了一个系统默认的值，在调用startActivity时会自动将该category添加到intent中

	intent中只能指定一个action，但可以指定多个category (使用 intent.addCategory("com.example.MY_CATEGORY"))方式添加，但此处指定的值必须在配置文件中设置，否则会引发异常：android.content.ActivityNotFoundException

3 其它隐式intent

~~~java
button.setOnClickListener(new View.OnClickListener() {
	@Override
	public void onClick(View v) {
		Intent intent = new Intent(Intent.ACTION_VIEW);
		intent.setData(Uri.parse("http://www.163.com"));
		// 打开拨号界面
		intent.setData(Uri.parse("tel:10086"));
		startActivity(intent);
	}
});
~~~
	
在配置文件中的intent-filter中可以配置data标签指定当前活动所响应的数据类型。

* android:scheme 指定协议部分，如http
* android:host	 指定主机名
* android:port	 指定端口号
* android:path	 指定端口号后的网址 
* android:mimeType 指定处理的数据类型，可使用通配符


### FAQ

1. Plugin with id 'com.novoda.bintray-release' not found的解决方法
	* 在你的根目录下的build.gradle中添加 dependencies { //添加下面这行代码就OK了 classpath 'com.novoda:bintray-release:0.3.4'  }
2. This Gradle plugin requires a newer IDE able to request IDE model level 3. For Android Studio this means version 3.0+ 导入android项目的时候，有时候会提示如上问题:
	* 在项目根目录的gradle.properties中加入下面这一句 android.injected.build.model.only.versioned=3
3. INSTALL_FAILED_TEST_ONLY: installPackageLI
	* 检查build.gradle中classpath是否是alpha版本
	* AndroidManifest.xml application标签中是否设置了android:testOnly=false
	* gradle.properties中加入android.injected.testOnly=false
4. Failed to resolve:com.android.support:appcompat-v7:报错处理
	* [原文](https://blog.csdn.net/mhl18820672087/article/details/78385361)
	* 原因: 
		- 当你在用别的电脑上的android studio编写一个项目时，然后copy下来，又在自己电脑上的android studio 上导入该项目时会报错（两台电脑上安装的android studio版本不一样）
		- 自己的android studio SDK平台工具的版本太低，然后在不了解项目构建文件（build.gradle文件）的前提下，点开了SDK Manger更新了项目构建工具（SDK Build-Tools）的版本
	* 解决方案
		- Settings -> Appearance & Behavior -> System Settings -> Updates 查看Android SDK Tools
		- 	
5. 解决Error:Could not determine the class-path for interface com.android.builder.model.AndroidProject.
	- [解决指南](https://blog.csdn.net/qq_21397217/article/details/65630730)