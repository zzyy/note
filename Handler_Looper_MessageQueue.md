### 基本的知识点
1. Looper对象使用ThreadLocal保存区分; 在每个线程直接用`Looper.myLooper`获取, 主线程的Looper可以用`Looper.getMainLooper`获取; 主线程的Looper有系统帮我们初始化, 在ActivityThread的main方法中, 系统会调用`Looper.prepareMainLooper`
1. 每个线程最多一个Looper对象; 否则会抛异常, 即不能调用2次Looper.prepare
1. 一个Looper对应一个MessageQueue实例;
1. 一个Handler对应一个Looper, 一个Looper可以有多个Handler去处理消息;
1. Handler初始化的时候,必须要有Looper对象; 即使调用Handler无参数的构造方法, Handler内部会用`Looper.myLooper`去获取Looper, 如果该线程没有准备Looper,此时会抛出异常
  > 子线程内部toast报错就是这个引起的; 在子线程Toast时候, Toast初始化时,内部调用new Handler(), 而子线程没有Looper, 就崩了


### 消息分发
