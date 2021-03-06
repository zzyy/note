---
title: 从fitSystemWindow说起
createAt: 2016-10-01
---

## 从fitSystemWindows说起

原先做状态栏颜色修改, 或者图片显示在状态栏之下, 一直都是copy代码, 没有具体去看, 最近有时间研究了下,涉及到的东西还是挺多的, 慢慢看,慢慢填坑

### 首先 fitSystemWindows 到底是有啥作用     
 首先看个链接,查找资料的时候,[同样看见有个人对此的困惑了, 在stackoverflow上提了个问题](http://stackoverflow.com/questions/31761046/what-exactly-does-fitssystemwindows-do);   
fitSystemWindows我个人的理解是,view有这个标志位之后,view会自动调整去适应系统UI,通俗的说,就是这个设置为true后, 在view在绘制时,会调整自身,为系统的状态栏,导航栏等空出位置, 让状态栏等不会盖住我们的布局;

这个标志,在4.4之前没怎么用过,基本没啥问题, 而在4.4后, 出现了沉浸试状态栏, 状态栏颜色, 后来又配合MD实现, 将View#fitSystemWindows() 扩展成 View#dispatchApplyWindowInsets() 和 View#onApplyWindowInsets()方法, 然后这个就变的复杂了 - -

fitSystemWindows有啥用,怎么用,有个Android的攻城师写了片文章的 https://medium.com/google-developers/why-would-i-want-to-fitssystemwindows-4e26d9ce1eec?linkId=19685562#.3a5ertaa7 ;国内已经有翻译了, 不过还是建议自己看原文, 我下面也主要是对这偏文章,结合我自己的理解和试验所写;

使用fitSystemWindows, 你要知道这几点
  - 它是用来告诉view, 为系统UI提供空间的, 免得让系统UI覆盖在我们的布局上了
  - fitSystemWindows按照深度优先的方式生效    
    各个拥有这个标志位的view,在被调用这个方法的时候是有顺序的(这功能感觉在5.0之后才凸显出作用,5.0添加了View#dispatchApplyWindowInsets()方法, 可以将WindowInsets分发下去, 让其他的view可以处理; 在之前,系统的view都是第一个被调用的view将它给消耗了,处理方式也简单粗暴,直接修改自身的padding留出空间, 后面的都不会有机会处理这个; 后面出的兼容包可以自己出处理这个问题);
  - Insets是相对与全屏幕来说的   
    Insets可能在Layout之前就要应用的,
  - View所有的padding都没被覆盖掉
    设置fitSystemWindows=true之后, 设置在这个view上的padding都会失效

  如果想让RecycleView的内容滚动到状态栏之下, 可以同时设置`android:fitsSystemWindows="true"`和`android:clipToPadding="false"`, 这样在布局初始化的时候,内容不会在状态栏之下, 滚动的时候, 内容可以滚到状态栏之下;
  >`android:clipToPadding="false"`的作用是是让padding的位置也可以用来绘制, clipToPadding默认是true

### 自定义fitsSystemWindows   
在4.4之下, 在在定义的view中重写`fitSystemWindows()`方法去实现自己的功能, 返回true, 代表你消耗了Insets, 否则返回false,让其他的view有机会去处理;

5.0后,废弃了'fitSystemWindows()'方法,用了2个新方法来扩展和简化流程;重写`onApplyWindowInsets()`方法,这个方法允许你只消耗部分Insets, 然后可以调用`dispatchApplyWindowInsets()` 让该View的子view去接着处理Insets;

另外,如果只在5.0之上使用,并不需要继承View,跟好的方法是使用`ViewCompat.setOnApplyWindowInsetsListener()`,在`ViewCompat`中也提供`onApplyWindowInsets()`和`dispatchApplyWindowInsets()`,这样可以免除写兼容代码的麻烦

##### read the fucking source code  
先看4.4的代码  

    //View#fitSystemWindows
    protected boolean fitSystemWindows(Rect insets) {
        if ((mViewFlags & FITS_SYSTEM_WINDOWS) == FITS_SYSTEM_WINDOWS) {
            mUserPaddingStart = UNDEFINED_PADDING;
            mUserPaddingEnd = UNDEFINED_PADDING;
            Rect localInsets = sThreadLocal.get();
            if (localInsets == null) {
                localInsets = new Rect();
                sThreadLocal.set(localInsets);
            }
            // conputeFitSystemWindow方法中, 只要有 FITS_SYSTEM_WINDOWS标志, 就会返回true
            boolean res = computeFitSystemWindows(insets, localInsets);
            // 简单粗暴, 直接设置padding
            internalSetPadding(localInsets.left, localInsets.top,
                    localInsets.right, localInsets.bottom);
            return res;
        }
        return false;
    }

    //在看下ViewGroup的#fitSystemWindows
    @Override
    protected boolean fitSystemWindows(Rect insets) {
        // 直接调用View的fitSystemWindows, 而在View中,只要有标志位,就会返回true
        boolean done = super.fitSystemWindows(insets);
        // 如果没消耗, 就给子view去处理
        if (!done) {
            final int count = mChildrenCount;
            final View[] children = mChildren;
            for (int i = 0; i < count; i++) {
                done = children[i].fitSystemWindows(insets);
                if (done) {
                    break;
                }
            }
        }
        return done;
    }

代码上看,4.4的代码处理方式很简单, 由于绝大部分View都没重写这个方法, 基本上是第一个有fitSystemWindows=true的view, 就把Insets给消耗了; 处理方式也很粗暴, 直接添加padding;

在`CoordinatorLayout`中, 会通过`dispatchApplyWindowInsetsToBehaviors`方法, 把Insets交给各个behave去处理
