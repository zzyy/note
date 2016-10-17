### ViewDragHelper
ViewDragHelper
  settleCapturedViewAt 设置被移动view的位置; 一般用于在drag释放后, 将view设置到某个位置
  findTopChildUnder 找到指定坐标的最上层view
  setMinVelocity  设置检测的最小速率;  设置后onViewReleased#onViewReleased回调, 如果速率达不到最小速率, 传入速率的值为0

ViewDragHelper.Callback
  - tryCaptureView  判断哪些view可以被移动
  - getViewHorizontalDragRange  返回水平移动的范围
  - clampViewPositionHorizontal 返回被移动的view 的left
  - onEdgeTouched 边缘被按下时触发的回调; 此时TouchEvent事件,并不会被ViewDragHelper给拦截; 在DrawLayout中, 发送了一个延迟消息,当按下一段时间后,Draw会显示一段出来,当ViewDragHelper设置setEdgeTrackingEnabled检测边缘后,边缘被触摸,就会回调;
  - onEdgeDragStarted 当边缘触摸滑动开始后调用, 一般需要调用captureChildView#captureChildView, 拦截TouchEvent同时捕获view;

        //用法
        //在onInterceptTouchEvent中判断是否需要拦截
        public boolean onInterceptTouchEvent(MotionEvent ev) {
            return ViewDragHelper#shouldInterceptTouchEvent( ev );
        }

        // 在onTouchEnent中处理
        @Override
        public boolean onTouchEvent(MotionEvent event) {
            mBottomDragger.processTouchEvent(event);
            // 注意需不需要拦截, viewGroup默认不会拦截down事件, 如果最先的down事件不处理, 后续的事件就不会被分发进来
            return true;
        }

        //复写conputerScroll, ViewDragHelper内部用Scroller实现,必须重写此方法
        @Override
        public void computeScroll() {
            super.computeScroll();
            if (ViewDragHelper#continueSettling(true)){
                ViewCompat.postInvalidateOnAnimation(this);
            }
        }

### GestureDetector
直接使用`GestureDetector`去帮忙处理事件
调用`onTouchEvent()`, 将事件交给`GestureDetector`去处理

### NestedScroll

##### NestedScrollingParent
##### NestedScrollingChild
