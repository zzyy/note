WeakLock 笔记
------

WeakLock可以用于阻止手机休眠, 防止后台任务由于休眠被打断; 保持屏幕常亮;

### 1.用法
        <uses-permission android:name="android.permission.WAKE_LOCK" />

        PowerManager powerManager = (PowerManager) getSystemService(POWER_SERVICE);
        WakeLock wakeLock = powerManager.newWakeLock(PowerManager.PARTIAL_WAKE_LOCK, "MyWakelockTag");
        wakeLock.acquire();
        
        if (wakeLock.isHeld()){
            wakeLock.release();
        }

注意点:    
- Wakelock 默认是引用计数，同一 wakelock 调用几次 acquire()，就需搭配调用几次 release() 才能真正释放
- 可以使用 `WakeLock.setReferenceCounted(false)` 关闭引用计数, 然后多次获取, 一次 release()
- 若 wakelock 已释放，再调用 release() 就会抛 RuntimeException异常
- 使用 `WakeLock.acquire(long timeout)`, 设定获取的时长, 超时后自动释放;

### 2.类型
在`powerManager.newWakeLock`时, 可以指定不同的 WakeLock 的类型, 以在不同的情况下使用;     


### 3.使用场景
1. Services 和 Receiver保持锁屏后运行; Receiver 可以使用系统提供的 `WakefulBroadcastReceiver`
2. Activity保持屏幕常亮, 不建议使用WakeLock, 可以使用其他方式, 如下;     
    - 在Window中添加保持常亮的 flag
            protected void onCreate(Bundle savedInstanceState) {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.activity_main);
                getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
            }
    - 在布局文件中设置
            <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:keepScreenOn="true" >
                ...
            </RelativeLayout>

#### 其他

1. 查看程序的 WakeLock

        adb shell dumpsys power
