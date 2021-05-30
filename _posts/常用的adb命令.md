- adb shell am broadcast 后面的参数有：

[-a

<action>]
[-d <data_uri>]
[-t <mime_type>]
[-c <category> [-c <category>] ...]
[-e|--es <extra_key>
  <extra_string_value> ...]
[--ez <extra_key>
  <extra_boolean_value> ...]
[-e|--ei <extra_key>
  <extra_int_value> ...]
[-n <component>]
[-f <flags>] [<uri>]</uri></flags></component></extra_int_value>
</extra_key></extra_boolean_value>
</extra_key></extra_string_value>
</extra_key></category></category></mime_type></data_uri></action>


		adb shell am broadcast -a com.android.test --es test_string "this is test string" --ei test_int 100 --ez test_boolean true    
		
		adb shell am start -a android.settings.APPLICATION_DEVELOPMENT_SETTINGS 
		          
		adb shell am start -n com.android.settings/.DevelopmentSettings		//打开开发者选项

### adb shell getprop 获取属性

### adb shell wm 获取屏幕相关信息
- adb shell wm size 查看分辨率<br>
  adb shell wm density 查看屏幕密度 > adb shell getprop ro.sf.lcd_density<br>
  adb shell sysdump window displays


### logcat
      格式 [adb] logcat [<option>].. [<filter-spec>]..

      [<filter-spec>] 过滤格式, 可以0个或者多个
      具体为 <tag>[:priority]
      '*' by itself means '*:D' and <tag> by itself means <tag>:V.

      eg. adb logcat zy:W *:E   // 显示tag为zy并且等级>W的log 和任何等级>E的tag

  参考  http://blog.csdn.net/xyz_lmn/article/details/7004710



### monkey
    -p 指定项目名, 可以指定一个或者多个包; 指定多个,需要多个 -p   
    -v 发送事件个数
    -s 伪随机数生成器的seed值; 如果用相同的seed值运行, 每次生成相同的事件序列
    --throttle 相邻事件的延迟; 通过这个可以减缓Monkey执行的速度

    eg. adb shell monkey -s 12 --throttle 450 -p com.android.cameraswitch -v 1000
    这条操作, 将对com.android.cameraswitch发送1000伪随机操作

  参考: http://www.jianshu.com/p/d5e1530cf152


### 查看apk的版本号信息等
		aapt dump badging xxx.apk
		
### 多台设备时, 指定某台设置
 1. 通过adb devices命令获取所有online设备的serial number
		# adb devices
		List of devices attached
		emulator-5566   host
		M161000555      device
 2. 通过adb -s <serial number> cmd向设备发送adb命令
		# adb -s M161000555 push M161000555 F:\


### 获取最上层Activity
`adb shell dumpsys activity | grep "mFocusedActivity"`
		