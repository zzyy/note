## app编译流程笔记
-----
### 编译步骤
1. 使用 aapt 生成 R.java 类文件
2. 使用 aidl.exe 将*.aidl文件转成 java 文件
3. 编译项目源码, 使用 javac 生成 class 文件
4. 使用 dx.bat 工具, 由 class 文件生成 dex文件
5. 使用 aapt 生成资源包文件; (此时已经生成一个.apk的文件, 但是不包含dex)
6. 将classes.dex 等打包进上一步生成的文件,生成成未签名的 apk
7. 使用 jarsigner 对 apk 进行签名
8. 使用 zipalign 工具,对签名后的.apk文件进行对齐处理（不进行对齐处理是不能发布到Google Market的）

用下面2张图来说明一下这个流程

| ![enter description here][1]    |     ![enter description here][2]|
| --- | --- |

### 手动编译app文件

#### 1. 使用AAPT编译生成R.java
aapt的可执行命令,在${ANDROID_SDK_HOME}/build-tools/${build_tool_version}目录下;    
执行命令: 
  
	aapt package -f -m -J .\build -S src\main\res -I D:\Android\adt-bundle-windows-x86_64-20140702\sdk\platforms\android-21\android.jar -M .\src\main\AndroidManifest.xml
	
	aapt 参数的含义
	-f 如果编译出来的文件已经存在，强制覆盖。
	-m 使生成的包的目录放在-J参数指定的目录。
	-J 指定生成的R.java的输出目录
	-S res文件夹路径
	-A assert文件夹的路径
	-M AndroidManifest.xml的路径
	-I 某个版本平台的android.jar的路径
	-F 具体指定apk文件的输出

#### 2.使用aidl编译AIDL文件 生成对应的java类文件
aidl的可执行命令,也在sdk的build-tools目录下,和aapt在相同的目录; 如果项目没有aidl文件,可不执行此步骤

#### 3.编译java文件,生成class文件
使用javac命令, 将项目源码,R.java和aidl生成的java文件,编译成class文件

	javac -encoding UTF-8 -bootclasspath D:\Android\adt-bundle-windows-x86_64-20140702\sdk\platforms\android-25\android.jar -d ./build/class .\build\com\eebbk\bfc\buildtest\R.java .\src\main\java\com\eebbk\bfc\buildtest\*.java

#### 4.将生成的class文件,转成dex文件
dx可执行命令,也在sdk的build-tools目录下; 

	dx --dex --output=.\build\classes.dex .\build\class  
	
#### 5.使用aapt生成资源包文件
此步骤,会将资源文件打包,会将生成的resources.arsc, Manifest文件, 图片等其他文件打包进apk里面

	aapt package -f -A .\src\main\assets -S .\src\main\res -M .\src\main\AndroidManifest.xml -I "D:\Android\adt-bundle-windows-x86_64-20140702\sdk\platforms\android-25\an
	droid.jar" -F .\build\test.apk

#### 6.将dex文件打包进apk
由于apkbuilder工具已弃用，我们需要调用sdklib.jar 里面的com.android.sdklib.build.ApkBuilderMain类去调用.

	java -classpath "D:\Android\adt-bundle-windows-x86_64-20140702\sdk\tools\lib\sdklib.jar" com.android.sdklib.build.ApkBuilderMain .\build\test_unsigned.apk -v -u -z .\
	build\test.apk  -f .\build\classes.dex

#### 7.签名

	jarsigner -verbose -keystore C:\Users\Administrator\.android\debug.keystore -storepass android -keypass android ".\build\test_unsigned.apk" androiddebugkey

#### 8.apk对齐

	 zipalign -f 4 ".\build\test_unsigned.apk" ".\build\test_signed.apk"



参考链接
1. https://www.oschina.net/question/54100_33877
2. http://www.wjdiankong.cn/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8ant%E8%84%9A%E6%9C%AC%E7%BC%96%E8%AF%91%E5%87%BAjar%E5%92%8Capk%E5%8C%85/
3. http://shinelw.com/2016/04/27/android-make-apk/


  [1]: https://www.github.com/zzyy/img/raw/master/app_build_0.png "app_build_0.png"
  [2]: https://www.github.com/zzyy/img/raw/master/app_build_1.png "app_build_1.png"
