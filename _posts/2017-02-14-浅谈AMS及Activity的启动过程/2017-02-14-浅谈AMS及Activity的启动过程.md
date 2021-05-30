createAt: 2017-02-14
-----

自己的一点笔记

### Activity的启动
平时调用startActivity启动一个Activity, 调用方法后, 发生了什么, ActivityManagerServices做了什么;

Activity A 启动Activity B, 总体来说可以分为一下几个步骤
1. 前期的部分校验, 然后通知AMS来启动Activity
2. AMS收到启动消息后, 在进行一下校验, 同时调整Activity任务栈
3. AMS通知Activity A暂停, 调用其onPause方法;
3. AMS通知Activity B所在的进程去启动Activity, 如果进程不存在, 还在创建其进程(即Application)
4. ActivityThread创建Activity B 并启动


> AMS调用Activity, 是通过IApplicationThread接口,向app发消息;  一般是调用 scheduleXxx方法, 然后通过Handler发送消息, 接下来调用 handleXxx方法, 然后调用performXxx方法, 在然后,调用Instrumentation的callActivityXxx方法, 最后调用Activity的performXxx方法, 然后回调各种onXxx方法
eg: 启动Activity;
ApplicationThread#scheduleLaunchActivity -> 
ActivityThread#handleLaunchActivity -> 
ActivityThread#performLaunchActivity ->
 mInstrumentation.callActivityOnCreate -> 
activity.performCreate -> 
activity.onCreate

### AMS
1. ActivityManagerServices是系统服务, 在系统启动后, 该服务就会起来; 
2. 通常在App中, 通过系统提供的ActivityManager的接口去调用;
3. AMS和平时写的AIDL一样, CS架构, 服务端是ActivityManagerService, 在ActiviyThread中, 通过其代理ActivityManagerProxy去调用

![](./pic/AMS_class.png)

- ActivityManagerNative : 和平时用AIDL生成的类一样, 有一个内部类ActivityManagerProxy, 通过Binder和远程的ActivityManagerService通讯; 并且有个getDefault方法, 提供一个IActivityManager的单例, 通过此去和AMS通讯;
- ActivityManager : 系统对外提供的AMS调用接口; 内部主要是通过 ActivityManagerNative.getDefault获取 AMS的proxy对象, 然后去和AMS通讯
- ActivityService : AMS的服务端
	1. 包含ActivityStackSupervisor mStackSupervisor, 通过其去管理整个Activity任务栈;
	2. 包含ArrayList<TaskRecord> mRecentTasks;![enter description here][1] 管理着最近运行的程序
	2. 包含ProcessMap<ProcessRecord> mProcessNames, 管理这所有的Application;
	3. 同时通过IApplicationThread和app进行通讯, 通过调用控制Activiy的生命周期; 
	
- ApplicationThread: 其整体结构和AMS类似; 
 1. 其服务端ApplicationThread在ActivityThread中, 即在app中; 
 3. 在app调用AMS时, 会把ApplicationThread对应的binder传递过去
 2. AMS通过ApplicationThreadProxy由Binder调用到app, 从而控制app的生命周期;
 
 -----
类图, 还没弄完, 有时间再来慢慢补
https://www.processon.com/view/link/58a3105ee4b006dc8c0ce1f0





