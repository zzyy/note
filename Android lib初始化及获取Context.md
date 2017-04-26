Android library初始化及获取Context
-------
### 开发中遇到的问题
开发library项目时, 经常需要获取Context对象, 并且需要在Application中初始化, 流程比较繁琐, 能否在lib中不依赖外部传入,主动获取到Context对象呢;

#### 解决办法
在lib中声明一个provider, 而在provider的onCreate方法中就可以获取Context对象, 可以保存起来, 在lib项目中使用;               
同时provider的初始化, 是在Application初始化之后, Application的onCreate方法调用之前, 此时也可以用于去初始化lib库;        

另一个方法就是通过反射调用ActivityThread, 获取 mInitialApplication 变量, 因为系统版本变化, 有风险, 需要自己做兼容;       

