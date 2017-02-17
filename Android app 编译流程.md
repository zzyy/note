## app编译流程笔记
-----
### 编译步骤
1. 使用 aapt 生成 R.java 类文件
2. 使用 aidl.exe 将*.aidl文件转成 java 文件
3. 编译项目源码, 使用 javac 生成 class 文件
4. 使用 dx.bat 工具, 由 class 文件生成 dex文件
5. 使用 aapt 生成资源包文件
6. 使用 apkbuilder 将资源文件, classes.dex 等打包成未签名的 apk
7. 使用 jarsigner 对 apk 进行签名
8. 使用 zipalign 工具,对签名后的.apk文件进行对齐处理（不进行对齐处理是不能发布到Google Market的）

用下面2张图来说明一下这个流程

| ![enter description here][1]    |     ![enter description here][2]|
| --- | --- |

### 手动编译app文件



参考链接
1. https://www.oschina.net/question/54100_33877
2. http://www.wjdiankong.cn/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8ant%E8%84%9A%E6%9C%AC%E7%BC%96%E8%AF%91%E5%87%BAjar%E5%92%8Capk%E5%8C%85/
3. http://shinelw.com/2016/04/27/android-make-apk/


  [1]: https://www.github.com/zzyy/img/raw/master/app_build_0.png "app_build_0.png"
  [2]: https://www.github.com/zzyy/img/raw/master/app_build_1.png "app_build_1.png"
