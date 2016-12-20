### TabLayout和TabLayout.Tab
1. 使用场景
	在ViewPager的上方，通常我们都会放一个标签指示器与ViewPager进行联动; 以前通常使用开源的框架,或者自定义view;现在google提供的解决办法

2. 使用方法

	通常我们调用`TabLayout.setupWithViewPager`和ViewPager进行关联, 每个Tab的内容从`PagerAdapter.getPageTitle`方法获取;
	
	也可以自己设置每个Tab的内容, 代码如下
	
		TabLayout tabLayout = ...;
		TabLayout.Tab tab = tabLayout.newTab();  // 创建tab
		tab.setText("Tab 1");		// 设置每个tab的属性
		tabLayout.addTab(tab);
	
	显示的模式有2种, 一种固定宽度`TabLayout.MODE_FIXED`, 一种可以滚动`TabLayout.MODE_SCROLLABLE`; 默认是`MODE_FIXED`, 可以通过代码,也可以通过xml属性设置
			
	常见属性
	
		<item name="tabMaxWidth">@dimen/tab_max_width</item>
		<item name="tabIndicatorColor">?attr/colorAccent</item>
		<item name="tabIndicatorHeight">2dp</item>
		<item name="tabPaddingStart">12dp</item>
		<item name="tabPaddingEnd">12dp</item>
		<item name="tabBackground">?attr/selectableItemBackground</item>
		<item name="tabTextAppearance">@style/TextAppearance.Design.Tab</item>
		<item name="tabSelectedTextColor">?android:textColorPrimary</item>
	
	https://guides.codepath.com/android/google-play-style-tabs-using-tablayout#design-support-library
	可以参考的[链接](http://www.jianshu.com/p/7f79b08f5afa)


## CoordinatorLayout  AppbarLayout CollapsingToolbarLayout
1. CoordinatorLayout作用
	CoordinatorLayout作用可以从名称看出,作为一个协调者; 主要用来协调调度子View; 分两个方面来协调, 协调子View的布局 和协调子View的事件分发

2. 解决的问题
	- 子View事件分发的困境; 
	 	在原先View的事件分发设计模型中, 事件由 dispatchTouchEvent分发, 然后由 onIntercepteTouchEvent负责拦截, 最后在onTouchEvent中处理, ActionDown事件一旦被接收, 后续事件将默认给此view处理, 其他view无法接收事件,  当有多个view需要同时处理一个滑动事件时, 原先的事件分发无法解决; 原先一旦遇到这种情况,只有自己去自定义view, 重写事件分发过程, 现在通过CoordinatorLayout可以优雅的解决这个问题;

		NestedScroll: 解决父子view的同时处理同一事件的流程     
		CoordinatorLayout: 解决并列的兄弟View处理同一事件的流程

	- 子View布局的困境
		和子view的事件分发一样, 原先每个子View布局位置, 是在父ViewGroup的layout方法中确定; 而当需要子view的布局位置, 跟随另一个子view变化时,绝大部分情况, 需要自定义view; 而现在CoordinatorLayout也可以优雅的解决这个问题
	
3. 使用
	使用很简单, 使用CoordinatorLayout作为父ViewGroup, 子view在xml中指定`app:behavior`; 指定的behavior都是继承于`CoordinatorLayout.Behavior`, Behavior类提供的接口中, 可以定义该子View需要和另外哪些子view关联, 关联的子view发生变化时, 自身怎么处理; 当父CoordinatorLayout接收到滑动事件时, 该子view怎么处理;

	Behavior需要自己去自定义, 不过系统默认提供几种常用的behavior; 具体介绍, 下面再说

4. 原理
	- 子view接收处理的事件如何交给父CoordinatorLayout去分发
		上面说过,父子view协同处理滑动事件, google官方很早给出了NestedScroll; 同时也在v4包中提供了`NestedScrillingChild`, `NestedScrollingChildHelper`, `NestedScrollingParent`和`NestedScrollingParentHelper`, 帮助我们解决嵌套的滚动问题;
		
		`CoordinatorLayout`实现的`NestedScrollingParent`接口, 因此当子view实现了`NestedScrillingChild`接口时,可以收到子view的滑动事件;  同时,原先系统的ScrollView, ListView都未实现`NestedScrillingChild`, 所以放在`CoordinatorLayout`中用于滚动的view一般是`RecyclerView`, `NestedScrollView`

	- 子view接收兄弟view的滑动事件并消耗掉
		上面说到子view把滑动事件交给CoordinatorLayout去处理, 子View接收消耗兄弟view的的事件,就是CoordinatorLayout接收到事件后, 调用兄弟view的behavior的方法, 由我们自定义behavior去处理

	- 子view跟随兄弟view变化而变化
		和上面一样, 布局原先是有layout方法决定, 而在`CoordinatorLayout`中, 在layout的过程中, 会调用behavior中的方法,以方便自己调整布局

> 从RecyclerView开始, View设计思想有所转变;  组合优于继承, 约定优于配置,体现的很明显    
> 多组合而少继承; 推荐扩展指定的组件实现功能, 而不是通过自定义view去实现功能; eg: 使用behavior去控制CoordinatorLayout的事件分发;使用LayoutMananger去控制recyclerview的布局


#### 和AppbarLayout, CollapsingToolbarLayout 结合使用
1. 作用
	AppbarLayout一般放在CoordinatorLayout中使用, 可以使不具有处理滚动事件的view, 产生滚动效果; AppBarLayout会默认和AppBarLayout.Behavior关联, 而该behavior中会帮我们处理滚动事件

	CollapsingToolbarLayout提供视差滚动, 和调整滚动时 状态栏和toolbar颜色配置的功能

2. AppbarLayout的使用
	AppBarLayout继承与LinearLayout, 可以为每个子view设置`app:layout_scrollFlags`属性, 用于去控制子view的滚动
	滚动属性有如下几种

	- scroll 该子view可以滚动
	- enterAlways  当有向下滚动事件时,改view就马上显示
	- scrollUntilCollpse  向上滚动搜索直至最小高度, 需要和minHeight一起用
	- snap 滚动只能在开始位置或者结束位置停下
		
		
		 <android.support.design.widget.CoordinatorLayout
		         xmlns:android="http://schemas.android.com/apk/res/android"
		         xmlns:app="http://schemas.android.com/apk/res-auto"
		         android:layout_width="match_parent"
		         android:layout_height="match_parent">
		
		     <android.support.v4.widget.NestedScrollView
		             android:layout_width="match_parent"
		             android:layout_height="match_parent"
		             app:layout_behavior="@string/appbar_scrolling_view_behavior">
		
		         <!-- Your scrolling content -->
		
		     </android.support.v4.widget.NestedScrollView>
		
		     <android.support.design.widget.AppBarLayout
		             android:layout_height="wrap_content"
		             android:layout_width="match_parent">
		
		         <android.support.v7.widget.Toolbar
		                 ...
		                 app:layout_scrollFlags="scroll|enterAlways"/>
		
		         <android.support.design.widget.TabLayout
		                 ...
		                 app:layout_scrollFlags="scroll|enterAlways"/>
		
		     </android.support.design.widget.AppBarLayout>
		
		 </android.support.design.widget.CoordinatorLayout>


> 一般在布局中, 遇到三段式布局, 并且布局布局要求滚动, 就可以考虑使用了

### SnakeBar  FloatActionButton
SnakeBar和Toast类似, 仅用于提示信息, 代码写法也和toast相似

	Snackbar.make(mCoordinatorLayout, "显示snackBar", Snackbar.LENGTH_SHORT).show();

	

### BottomSheetBehavior  BottomSheetDialog
使View从底部弹出, 分两段显示的一个Coordinator.Behavior, 必须使用在`CoordinatorLayout`中;

对需要使用的的View,在布局中添加上`app:layout_behavior="@string/bottom_sheet_behavior"`属性即可

常用的有3中模式状态, 展开`STATE_EXPANDED`, 收缩`STATE_COLLAPSED`, 隐藏`STATE_HIDDEN`, 隐藏后,一般需要通过代码使其展开

在代码中使用

	View view = ....;
	final BottomSheetBehavior behavior = BottomSheetBehavior.from(view);
	behavior.setState(BottomSheetBehavior.STATE_COLLAPSED)		// 设置状态

常用属性

	app:behavior_hideable="true"		// 是否可以隐藏
    app:behavior_peekHeight="100dp"		// 坍塌时的高度
		
> BottomSheetDialog是BottomSheet的dialog实现形式; 实现比较巧妙, BottomSheetDialog继承于Dialog,在构造时,会创建CoordinatorLayout, 然后拿到dialog对应的window的decorView, 将其添加到CoordinatorLayout中, 从而实现BottomSheetBehavior


### Ripple animattion
水波纹动画目前仅支持api>21的版本
属于view的背景效果,放在drawable文件下, 由于只支持21之上,一般需要针对不同的版本提供2套背景, drawable-v21目录使用水波纹悲剧, drawable目录使用普通背景

创建内容很简单

		<ripple xmlns:android="http://schemas.android.com/apk/res/android"
			android:color="?android:colorControlHighlight">
			<item android:drawable="@drawable/button_normal" />
		</ripple>
		
有无界`unbounded`和有边境之分, 如果ripple中间没有item, 就为unbounded的, 水波纹可以扩散到其他view的背景中去

### Reveal animator
揭露动画, 目前也仅支持api>21

通过`ViewAnimationUtils.createCircularReveal`创建, 返回的是一个`Animator`对象;

		// 创建揭露动画, 
		Animator revealAnimator = ViewAnimationUtils.createCircularReveal( animatorView, x, y, 0, animatorView.getWidth() );

### stateListAnimator
View的状态动画, 和view的状态背景相似
动画文件放在animator目录下, 

参考[链接](http://blog.chengyunfeng.com/?p=1014)

### Transtions 转场动画
新的过渡动画,仍然只兼容api>21的版本
示例demo可以参考 https://github.com/lgvalle/Material-Animations

#### 过渡动画
原先的过渡动画`Activity.overridePendingTransition`,只有进入,退出两种, 现在有了4种;

分别为以下4种;
EnterTransition <--> ReturnTransition
ExitTransition <--> ReenterTransition

假设Activity A 启动Activity B;    
A退出时,触发ExitTransition; 跳转到B, 进入到B时, 触发EnterTransition;    
从B点击返回键,退回到A时    
B触发ReturnTransition, 返回到A时, 触发A的ReenterTransition

目前支持的动画, 有Explode, Fade, Slide几种, 动画支持在主题中配置,和使用代码设置, 和原先的过渡动画相似

 - 在主题中配置动画示例
 
	1. 创建动画文件
			<?xml version="1.0" encoding="utf-8"?>
			<slide xmlns:android="http://schemas.android.com/apk/res/"
				android:duration="1000"/>
	2. 在主题中使用属性引用动画文件
			<item name="android:windowEnterTransition"></item>
	        <item name="android:windowExitTransition"></item>
	        <item name="android:windowReenterTransition"></item>
	        <item name="android:windowReturnTransition"></item>
			
 - 手动配置示例
		@Override
		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);
			setContentView(R.layout.activity_transition);
			setupWindowAnimations();
		}
	
		private void setupWindowAnimations() {
			Slide slide = TransitionInflater.from(this).inflateTransition(R.transition.activity_slide);
			getWindow().setExitTransition(slide);
		}
	
> 如果只配置EnterTransition,没有配置ReturnTransition, 在返回时, 将会把EnterTransition反过来运行一遍

> 退出时需要运行ReturnTransition和ReenterTransition试, 不能直接调用`Activity.finish`方法, 要改为调用`Activity.finishAfterTransition`, 如果各版本要保持相同的代码, 可以调用`ActivityCompact`的`ActivityCompat`方法

#### 共享元素的过渡动画
1. 在主题中打开该功能, 如果使用的material design主题, 会默认打开
	<style name="MaterialAnimations" parent="@style/Theme.AppCompat.Light.NoActionBar">
		...
		<item name="android:windowContentTransitions">true</item
		...
	</style>
2. 设置共享view的transition name; 2个共享view的android:transitionName属性必须一样
		<ImageView
			android:id="@+id/small_blue_icon"
			style="@style/MaterialAnimations.Icon.Small"
			android:src="@drawable/circle"
			android:transitionName="@string/blue_name" />
3. 使用新的api构建动画并启动actiivity
		blueIconImageView.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View v) {
				Intent i = new Intent(MainActivity.this, SharedElementActivity.class);

				View sharedView = blueIconImageView;
				String transitionName = getString(R.string.blue_name);

				ActivityOptions transitionActivityOptions = ActivityOptions.makeSceneTransitionAnimation(MainActivity.this, sharedView, transitionName);
				startActivity(i, transitionActivityOptions.toBundle());
			}
		});

> 如果想要保持不同版本的代码一致, 可以使用v4包中的`ActivityCompat.startActivity`方法去启动activity

