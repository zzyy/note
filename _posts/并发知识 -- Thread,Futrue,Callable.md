多线程 -- 线程相关基础
-----

# 1. 线程状态
- New (新生): 创建进程之后, 还未启动之前;
- Runable (可运行): 调用start方法之后;
- Blocked (被阻塞)
- Waitting (等待)
- Time waiting (计时等待)
- Terminated (中止退出)

## 1.1 新生状态
实例化一个线程后, 线程还没运行之前; 即创建线程 `new Thread()`, 未调用`start()`方法之前都是新生状态

## 1.2 可运行状态
一旦调用start方法, 线程就进入了runnable状态了; 此时线程可能在等待运行(等待系统分配cpu资源), 或者正在运行;
> 有些文章将等待运行和正在运行分为2种状态(runnable和running状态), 但是Java规范中,都是作为runnale状态;

## 1.3 被阻塞线程和等待线程
当线程处于被阻塞或者等待状态时, 将暂时停止活动; 此时不运行任何代码且消耗最少的资源, 直到线程调度器重新激活它;
- 当一个线程试图获取对象的内部锁, 而该锁被其他对象持有时, 线程进入阻塞状态;
- 当线程等待另外一个线程的通知调度器的一个条件时, 进入等待状态; 例如调用`Object.wait`方法或者`Thread.join`方法, 或者是等待`java.util.concurrrent`库中的Lock或Condition时;
- 等待方法设置了超时参数,即进入了计时等待状态; 例如`Thread.sleep`方法, `Objec.wait(100)`等

## 1.3 中止状态
下面2个原因之一使进程进入中止状态
- run方法运行结束,正常退出而自然死亡
- 由于没有捕获的异常中止了run方法而以外死亡

> 线程状态可以通过`Thread.getState`方法获取;

# 线程优先级
每一个线程,有一个默认的优先级; 可以使用`setPriority`方法设置;
