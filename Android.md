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



