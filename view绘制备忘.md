### 自定义view
一般用来扩展, 比较重要的毁掉
  - onFinishInflate
  - onSizeChanged
  - onMeasure
  - onLayout
  - onTouchEvent
  - generateDefaultLayoutParams 生成被添加进ViewGroup的LayoutParams; 可以重写此方法, 生成自定义的LayoutParams
  - getChildMeasureSpec 快速生成子view的测量值, 用于去测量子view; 调用子view的measure

measure
  onMeasure 中确定view自身的测量大小 并完成子view的测量
  setMeasuredDimension 设置大小

  remeasure
    RelativeLayout LinearLayot拥有weight的
    一旦开始measure; 该view的子view都会被重新layout
layout
  layout确认view自身的位置, onLayout则确定所有子view的位置

draw
  - 绘制背景
  - 绘制自己 onDraw
  - 绘制子view  dispatchDraw
  - 绘制装饰  onDrawScrollBars

> `setWillNotDraw`, 通过这个方法,告诉系统该view不需要绘制; view本身没有设置这个标志位, 但ViewGroup设置过, 自定义view继承viewGroup,没有绘制功能时, 需要自己设置

1. 减少overDraw
2. 布局扁平化, 减少层级
    减少remeasure;
3. viewStub 按需添加
4. merge减少一个层级
