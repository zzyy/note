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
`adb shell dumpsys meminfo -d <package_name|pid>`     

```

PS C:\Users\Administrator> adb -s 7d3644df shell dumpsys meminfo -d 10108
Applications Memory Usage (in Kilobytes):
Uptime: 151286952 Realtime: 283766212

** MEMINFO in pid 29080 [com.eebbk.bfc.demo.component] **
                   Pss  Private  Private  SwapPss     Heap     He
                 Total    Dirty    Clean    Dirty     Size    All
                ------   ------   ------   ------   ------   ----
  Native Heap     5963     5900       20        0    14336    112
  Dalvik Heap     2033     1808      172        0    31830    191       // Dalvik 分配占用的 RAM, PssTotal包含Zygote的占用, PrivateDirty是分配到App堆的
 Dalvik Other      541      540        0        0
        Stack      300      300        0        0
       Ashmem        2        0        0        0
      Gfx dev     1218     1216        0        0
    Other dev        8        0        8        0
     .so mmap     1425      120      264       92       
    .jar mmap        0        0        0        0
    .apk mmap      355        0      280        0
    .ttf mmap       92        0       84        0
    .dex mmap     1840        8     1828        0
    .oat mmap     3976        0     1784        0
    .art mmap     2717      936      928        5
   Other mmap      143        4       12        0
   EGL mtrack    14336    14336        0        0
    GL mtrack     4296     4296        0        0
      Unknown      690      684        0        3
        TOTAL    40035    30148     5380      100    46166    303

 Dalvik Details
        .Heap     1528     1528        0        0       // 应用的堆内存, 不包括映像中的对象和大型对象空间，但包括 zygote 空间和非移动空间
         .LOS       27        8        0      259       // ART 大型对象空间占用的 RAM 量, 包括 zygote 大型对象
 .LinearAlloc      237      236        0        0
          .GC      224      224        0        0
    .JITCache       16       16        0        0      
      .Zygote      442      236      172        2       // zygote 空间占用的内存量,  zygote 空间在设备启动时创建且永远不会被分配
   .NonMoving       36       36        0        0       // ART 非移动空间占用的 RAM 量; 方法区(Non-heap) 包含字段和方法,
 .IndirectRef       64       64        0        0       // ART 间接引用表占用的 RAM 量; 可以通过减少使用的本地和全局 JNI 引用数量来减少此 RAM 量

 App Summary
                       Pss(KB)
                        ------
           Java Heap:     3672
         Native Heap:     5900
                Code:     4368
               Stack:      300
            Graphics:    19848
       Private Other:     1440
              System:     4507

               TOTAL:    40035       TOTAL SWAP PSS:      100
               // TOATAL: 进程占用的PSS总量, 可以用来和其他App比较, 也可以用来计算进程内存占总内存的比重

 Objects
               Views:       11         ViewRootImpl:        1
         AppContexts:        3           Activities:        1
              Assets:        5        AssetManagers:        2
       Local Binders:        9        Proxy Binders:       16
       Parcel memory:        5         Parcel count:       21
    Death Recipients:        1      OpenSSL Sockets:        0

 SQL
         MEMORY_USED:        0
  PAGECACHE_OVERFLOW:        0          MALLOC_SIZE:        0

```

> dumpsys meminfo 也可以不跟包名, 查看所有进程的内存信息; 

## procrank
procrank 需要有root权限             
可以所有进程的内存使用的排行榜，排行是以Pss的大小而排序   

```
PS C:\Users\Administrator> adb -s M161000555 shell
shell@M1000:/ $ su
root@M1000:/ # procrank
  PID       Vss      Rss      Pss      Uss     Swap    PSwap  cmdline
  934  2696804K  170764K  110772K  103132K   35988K     999K  system_server
26034  3263568K  137608K   79455K   72512K   35832K     961K  com.android.systemui
 1831  2856512K  115168K   60995K   57096K   36168K     970K  com.bbk.studyos.launcher
 6809  1160240K   91036K   48164K   43336K   19540K    1142K  com.sohu.inputmethod.sogouoem
13843  1714868K   87716K   46133K   43364K   36368K     977K  android.process.acore
 1985  2439616K   91188K   36155K   31968K   36144K     969K  com.eebbk.bbkmiddlemarket
 3747  1691016K   75044K   29061K   22796K   19580K    1144K  com.UCMobile:push
 1129  2400888K   71080K   27289K   24708K   36324K     979K  com.eebbk.bbksafe:adfilter
 1696  1134336K   62096K   26934K   24464K   19872K    1187K  com.mediatek.voicecommand
 9949  2017960K   78196K   24590K   20852K   36172K     970K  com.android.settings
 ...
  295    13972K     956K      20K       4K     424K     424K  /system/bin/sh
                          ------   ------   ------  ------
                         987095K  836172K  1787396096K  TOTAL

```       


###### 参考
1. https://developer.android.com/studio/profile/investigate-ram.html
2. http://gityuan.com/2016/01/02/memory-analysis-command/