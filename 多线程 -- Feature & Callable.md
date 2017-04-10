多线程 -- Future & Callable
-----
## Callable
Callable 和 Runnable作用类似, 都是为了让其实例给另一个Thread去执行,提交给线程池去执行;       
但是Runnable无返回值并且无法抛出异常, 而Callable可以返回结果;

## Future
Feature 就是上面 Callable 提交给线程池后, 异步计算的结果;   
提供任务是否完成, 取消任务, 获取任务结果的接口;      
使用`get`方法获取结果, 大部分实现类, 实现了获取异步计算结果前,阻塞当前线程; 因而该功能可以用于当前线程去同步获取异步计算结果;    

## FeatureTask
该类实现了 Future 和 Runnable 接口;         
异步任务可以调用 `set`, `setException`方法, 将计算结果返回;      