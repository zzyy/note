# 内存命令

## 指标概念

item | 名称 | 说明 | 计算
---- | ---- | ----| ----
Uss | Unique Set Size | 进程独占的物理内存
Pss | Proportional Set Size | 按比例分配的内存(包含按比例分配的由多进程共享的库的内存) | Pss = Uss + 按比例计算的共享内存
Rss | Resident Set Size | 包含共享库所占的实际内存 | Rss = Uss + 共享内存
Vss | Vitual Set Size | 虚拟耗用的内存 | Vss = Rss + 为实际分配的物理内存

> 一般使用Pss来衡量当前App使用的内存; 将所有进程的Pss相加,等于所有进程正在使用的实际内存; 适合测定进程内存在总RAM中的比重, 同时适合和其他App比较内存占用;

## 命令

### dumpsys meminfo
`adb shell dumpsys meminfo <package_name|pid> [-d]`     




###### 参考
1. https://developer.android.com/studio/profile/investigate-ram.html
2. http://gityuan.com/2016/01/02/memory-analysis-command/