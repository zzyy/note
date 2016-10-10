ViewDragHelper
  settleCapturedViewAt 设置被移动view的位置; 一般用于在drag释放后, 将view设置到某个位置
  findTopChildUnder 找到指定坐标的最上层view

ViewDragHelper.Callback
  - tryCaptureView  判断哪些view可以被移动
  - getViewHorizontalDragRange  返回水平移动的范围
  - clampViewPositionHorizontal 返回被移动的view 的left
  - onEdgeTouched 边缘被按下时触发的回调; 此时TouchEvent事件,并不会被ViewDragHelper给拦截; 在DrawLayout中, 发送了一个延迟消息,当按下一段时间后,Draw会显示一段出来,当ViewDragHelper设置setEdgeTrackingEnabled检测边缘后,边缘被触摸,就会回调;
  - onEdgeDragStarted 当边缘触摸滑动开始后调用, 一般需要调用captureChildView#captureChildView, 拦截TouchEvent同时捕获view;
