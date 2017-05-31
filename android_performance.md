## 内存
- 目的: 无内存泄漏, 降低内存开销, 避免内存抖动; 减少非并发GC的运行次数
- 工具: LeakCanary, Memory Monitor, MAT, Allocation Tracker
1. 复用
    Object Pool, BitmapFactory.Options.inBitmap, Cache, onDraw/for, convertView

2. 回收
    register/unregister, Cursor/Bitmap/WebView/TypeArray, Handler, onTrimMemory

3. 减少开销
    SparseArray, no-Enum, Bitmap-scale/format

4. 扩大内存
    largeHeap, mutil-process

## View
- 目的: 界面流畅性, 无掉帧, 避免卡顿
- 工具: Show GPU Overdraw, Profile GPU rendering, Layout Inspector, Hierarchy Viewer, Traceview,
1. 重用
    include

2. 过度绘制
    setWindowDrawable(null), merge, canvas.clipRect

3. 渲染速度, Jank
    viewStub, merge, double layout taxation(RelativeLayout)

## 启动
- 目的: 加快启动速度, 避免白屏
- 工具: Systrace

