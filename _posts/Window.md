1. Window是一个抽象类, 具体的实现是PhoneWindow
2. android系统中, 每个界面,对应着一个window; 但其实在android系统中window也是一个抽象的概率,它是以view的形式存在; 在使用中, 无法直接访问Window, 只能通过WindowManager才能访问Window; 每个Window都对应一个View和一个ViewRootImpl; WMS的一些消息,通过ViewRootImpl转发给View;
3. WindowManager继承自ViewManager，常用的只有三个方法：addView、updateView和removeView

# Activity 的window添加

Activity创建之后, 在ActivityThread中 调用 Activity#attach, 进行一些初始化操作

```java
final void attach(Context context, ActivityThread aThread,
        Instrumentation instr, IBinder token, int ident,
        Application application, Intent intent, ActivityInfo info,
        CharSequence title, Activity parent, String id,
        NonConfigurationInstances lastNonConfigurationInstances,
        Configuration config, String referrer, IVoiceInteractor voiceInteractor) {

    attachBaseContext(context);
    ....
    //创建对应的Window 并设置callback, 其实为PhoneWindow
    mWindow = new PhoneWindow(this);
    mWindow.setCallback(this);
    mWindow.setOnWindowDismissedCallback(this);
    ....
    // 设置Window的WindowManager, 对Window的mWindowManager赋值,
    // 事实上, Window中 并未使用传递进去的windowManager, 而是在此方法中 调用WindowManagerImpl.createLocalWindowManager 重新创建了一个
    mWindow.setWindowManager(
            (WindowManager)context.getSystemService(Context.WINDOW_SERVICE),
            mToken, mComponent.flattenToString(),
            (info.flags & ActivityInfo.FLAG_HARDWARE_ACCELERATED) != 0);
    if (mParent != null) {
        mWindow.setContainer(mParent.getWindow());
    }
    // 把window对象中的 windowManager 关联到 Activity的 mWindowManager
    mWindowManager = mWindow.getWindowManager();
    ...
  }
```

在Activity#attach中, 可以看出创建了一个PhoneWindow对象, 并且通过`context.getSystemService(Context.WINDOW_SERVICE)`设置了WindowManager对象, 还通过Window#getWindowManager对Activity的mWindowManager赋值, 这样 Activity的Window WindowManager就关联起来了;

接着看上面代码,里面会通过context.getSystemService(Context.WINDOW_SERVICE)拿到一个windowManager对象, 这个window对象是什么呢? 如果对Context有过研究的话, 可以知道Context的实际对象是ContetImpl, 其中所有通过 getSystemService返回的对象, 都是通过static的代码块静态添加的, 只需找WINDOW_SERVICE注册的地方(在6.0中 注册的代码 已经提取到`android.app.SystemServiceRegistry#registerService`中了)

```java
registerService(Context.WINDOW_SERVICE, WindowManager.class,
               new CachedServiceFetcher<WindowManager>() {

           @Override
           public WindowManager createService(ContextImpl ctx) {
               return new WindowManagerImpl(ctx.getDisplay());
           }});
}
```

可以看见,实际的对象是WindowManagerImpl; (另外 Activity本身重写了getSystemService方法,如果使用android.app.Activity#getSystemService, 返回的其实不是这个对象, 下面会说到);

上面注释说到, android.view.Window#setWindowManager中, 并未使用传递进去的WindowManager,而创建了一个新对象, 可以看一下代码

```java
public void setWindowManager(WindowManager wm, IBinder appToken, String appName,
            boolean hardwareAccelerated) {
        // activity把token传进来, 保存在Window.mAppToken
        mAppToken = appToken;
        mAppName = appName;
        mHardwareAccelerated = hardwareAccelerated
                || SystemProperties.getBoolean(PROPERTY_HARDWARE_UI, false);
        if (wm == null) {
            wm = (WindowManager)mContext.getSystemService(Context.WINDOW_SERVICE);
        }
        //创建了一个新的WindowManager, 并且把this 传递给了WindowManager
        mWindowManager = ((WindowManagerImpl)wm).createLocalWindowManager(this);
    }
```


而在android.view.WindowManagerImpl#createLocalWindowManager中, 可以明显看出
```java
// Context.getSystemService 获得的WindowManager实例, 是没有parentWindow的
public WindowManagerImpl(Display display) {
        this(display, null);
    }

private WindowManagerImpl(Display display, Window parentWindow) {
    mDisplay = display;
    mParentWindow = parentWindow;
}

public WindowManagerImpl createLocalWindowManager(Window parentWindow) {
    // 将传递的 window对象保存, 对于activity来说,会将PhoneWindow对应的对象传入, 而对于 Context.getSystemService 获得的WindowManager实例, 是没有parentWindow的
    return new WindowManagerImpl(mDisplay, parentWindow);
}
```


window时我们看见的窗口,接下来看在Activity的window是怎么显示出来, 看android.app.ActivityThread#handleResumeActivity

```java
final void handleResumeActivity(IBinder token,
        boolean clearHide, boolean isForward, boolean reallyResume) {
          ....
          if (r.window == null && !a.mFinished && willBeVisible) {
                r.window = r.activity.getWindow();
                // activity中 PhoneWindow的decorView, 即activity的根view
                View decor = r.window.getDecorView();
                decor.setVisibility(View.INVISIBLE);
                // 获取Activity的windowManager
                ViewManager wm = a.getWindowManager();
                // 设置activity对应的window的type
                WindowManager.LayoutParams l = r.window.getAttributes();
                a.mDecor = decor;
                l.type = WindowManager.LayoutParams.TYPE_BASE_APPLICATION;
                l.softInputMode |= forwardBit;
                if (a.mVisibleFromClient) {
                    a.mWindowAdded = true;
                    // windowManger中 添加window的根view( 即 decorView)
                    wm.addView(decor, l);
                }
              }
            .....
}
```

到此, Activity对应的decorView就被添加进去了;

Activity.makeVisiable

```java
void makeVisible() {
    if (!mWindowAdded) {
        ViewManager wm = getWindowManager();
        // windowManger中 添加window的根view( 即 decorView)
        wm.addView(mDecor, getWindow().getAttributes());
        mWindowAdded = true;
    }
    mDecor.setVisibility(View.VISIBLE);
}
```

上面已经说过, WindowManager对应的实例是WindowManagerImpl, 接着看android.view.WindowManagerImpl#addView

```java
 private final WindowManagerGlobal mGlobal = WindowManagerGlobal.getInstance();

@Override
public void addView(@NonNull View view, @NonNull ViewGroup.LayoutParams params) {
    applyDefaultToken(params);
    mGlobal.addView(view, params, mDisplay, mParentWindow);
}
```

代码很简单, 最后调用的是 WindowManagerGlobal#addView, 并且WindowManagerGlobal是一个单例, 看下android.view.WindowManagerGlobal#addView的代码

```java
public void addView(View view, ViewGroup.LayoutParams params,
            Display display, Window parentWindow) {
              ...
         // 首先获得 layoutParams
        final WindowManager.LayoutParams wparams = (WindowManager.LayoutParams) params;
        if (parentWindow != null) {
            // parentWindow是 WindowManagerImpl的成员变量mParentWindow;
            // 对于 activity来说, 即在mWindow.setWindowManager中 调用WindowManagerImpl.createLocalWindowManager(Window)去创建 mWindowManager实例时, 传入的phoneWindow对象
            // 调用下面这个方法, 会调整 WindowManager.LayoutParams, 主要是 设置 token 和硬件加速的标志位
            parentWindow.adjustLayoutParamsForSubWindow(wparams);
        } else {
            // If there's no parent, then hardware acceleration for this view is
            // set from the application's hardware acceleration setting.
            final Context context = view.getContext();
            if (context != null
                    && (context.getApplicationInfo().flags
                            & ApplicationInfo.FLAG_HARDWARE_ACCELERATED) != 0) {
                wparams.flags |= WindowManager.LayoutParams.FLAG_HARDWARE_ACCELERATED;
            }
        }

        ViewRootImpl root;
        View panelParentView = null;
        ....
        // ViewRootImpl是一个很重要的类, 是ViewParent的子类, 可以说是Window和WMS之间的桥梁, 所有和View有关的Evnet,layout,draw,都在底层产生后通过WMS->ViewRootImpl传递给view
        root = new ViewRootImpl(view.getContext(), display);

        view.setLayoutParams(wparams);

        mViews.add(view);
        mRoots.add(root);
        mParams.add(wparams);

        ...
        root.setView(view, wparams, panelParentView);
        ...
}
```

在WindowManagerGlobal中, 会为每一个window创建对应的ViewRootImpl, 并把对应的 decorView, ViewRootImpl 和 WindowManager.LayoutParams 保存, 最后把decorView 通过android.view.ViewRootImpl#setView添加进WMS 妈蛋,好累啊, 终于快到WMS了

接着看一下android.view.ViewRootImpl#setView

```java
public void setView(View view, WindowManager.LayoutParams attrs, View panelParentView) {
      ...
      // 把Window对应的View保存下来, 对应的其实就是 PhoneWindow的 mDecorView
      mView = view;
      ...
      // 调用WMS后返回的结果
     int res; /* = WindowManagerImpl.ADD_OKAY; */

     // Schedule the first layout -before- adding to the window
     // manager, to make sure we do the relayout before receiving
     // any other events from the system.
     // 请求 layout
     requestLayout();
    ....

    // 调用 WMS添加window, 并返回一个结果 用于判定添加的结果
    res = mWindowSession.addToDisplay(mWindow, mSeq, mWindowAttributes,
                            getHostVisibility(), mDisplay.getDisplayId(),
                            mAttachInfo.mContentInsets, mAttachInfo.mStableInsets,
                            mAttachInfo.mOutsets, mInputChannel);
        ......                              

    // 根据返回的结果 判断是否添加成功, 没成功 就throw exception, 经常看见的services打开dialog的异常 就在这儿产生
    if (res < WindowManagerGlobal.ADD_OKAY) {
        mAttachInfo.mRootView = null;
        mAdded = false;
        mFallbackEventHandler.setView(null);
        unscheduleTraversals();
        setAccessibilityFocus(null, null);
        switch (res) {
            case WindowManagerGlobal.ADD_BAD_APP_TOKEN:
            case WindowManagerGlobal.ADD_BAD_SUBWINDOW_TOKEN:
                throw new WindowManager.BadTokenException(
                        "Unable to add window -- token " + attrs.token
                        + " is not valid; is your activity running?");    
            .....
          }                          
     ......
}
```

ViewRootImpl里面, 首先会把window对应的decorView存一份, 然后通过mWindowSession向WMS发消息, mWindowSession是通过WindowManagerGlobal的静态方法获得, 前面说过,这玩意是一个单例, 在哪儿都可以拿到; android本身是一个CS架构, 调用WMS,需要先获取WMS对应的client, 而client端的获取,就是同 WindowManagerGlobal.initialize()进行, 连通WMS的服务端; android.view.WindowManagerGlobal#getWindowSession, 是通过getWindowManagerService().openSession()获得, 最后返回的是com.android.server.wm.Session, 而mWindowSession.addToDisplay最终调用的会是 com.android.server.wm.Session#addToDisplay, 最终会还是会调用com.android.server.wm.WindowManagerService#addWindow; 这一段在WMS Server端的代码也比较多, 就不贴代码了, 最后在WindowManagerService#addWindow, 会对WindowManager.LayoutParams做一些检验 并返回值, 下面会说到;

上面说过事件是通过WMS传递给ViewRootImpl,然后传递给View,Activity, 具体事件在ViewRootImpl的分发过程, 可以看这篇博客[here](http://blog.csdn.net/singwhatiwanna/article/details/50775201)

有些手机上弹框需要权限,通过`TYPE_TOAST`突破权限的分析 [here](http://www.jianshu.com/p/167fd5f47d5c/comments/776666), 和[here](http://www.liaohuqiu.net/cn/posts/android-windows-manager/)
