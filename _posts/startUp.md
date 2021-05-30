## 启动流程?

1. init
2. 启动本地服务
    daemons, runtime -> ServerManager,  Zygote
3. Zygote启动虚拟机, 然后启动java服务
    Zygote -> Dalvik VM -> SystemServer  -> SurfaceFlinger  -> 各种服务java端 (AMS, PMS等)
