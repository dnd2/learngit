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

