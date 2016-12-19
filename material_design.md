### TabLayout  TabLayout.Tab
在ViewPager的上方，通常我们都会放一个标签指示器与ViewPager进行联动;

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


### CoordinatorLayout  CollapseLayout AppbarLayout  RecyclerView

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
		
BottomSheetDialog是BottomSheet的dialog实现形式; 实现比较巧妙, BottomSheetDialog继承于Dialog,在构造时,会创建CoordinatorLayout, 然后拿到dialog对应的window的decorView, 将其添加到CoordinatorLayout中, 从而实现BottomSheetBehavior


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

在主题中配置动画示例
	1. 创建动画文件
		<?xml version="1.0" encoding="utf-8"?>
		<slide xmlns:android="http://schemas.android.com/apk/res/"
			android:duration="1000"/>
	2. 在主题中使用属性引用动画文件
		<item name="android:windowEnterTransition"></item>
        <item name="android:windowExitTransition"></item>
        <item name="android:windowReenterTransition"></item>
        <item name="android:windowReturnTransition"></item>
			
手动配置示例
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

