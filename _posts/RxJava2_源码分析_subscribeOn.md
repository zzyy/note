observeOn 
------

        Observable.create((ObservableOnSubscribe<Integer>) e -> {
            System.out.println("observable : " + Thread.currentThread());
            e.onNext(1);
        })
                .subscribeOn(Schedulers.single())
                .subscribe(integer -> {
                    System.out.println(integer);
                    System.out.println("observer:  " + Thread.currentThread());
                });

Rxjava默认是在当前线程生发送事件, `subscribeOn`可以切换Observable发送事件所在的线程;     
如果没有使用ObserveOn指定消费事件的线程, Observer将在Observable发送事件的的线程, 消费事件;

源码分析目的:    
1. Schduler 作用
2. subscribeOn 做了什么


### 1. Schduler
Schduler不好直接用代码解释, 先说结论, 后面再去具体代码分析;
1. 切换线程, 需要提供对应的Schduler; 
2. Schduler可以通过`createWorker`方法, 创建一个`Worker`类的实例;
3. Worker有一个`schedule`方法, 提交runnable去运行; 切换线程, 就是把各个onNext的调用方法,封装成一个runnable 提交到指定线程去运行;
4. 通过`Worker.schedule`提交runnable后, 会返回一个disposable对象, 用于取消或控制Observale的发射任务;
5. `Schduler`本身是个管理类, 一般内部会创建具体的线程池, 同时通过统一的`start` `shutdown`等方法管理着线程池
6. `Schduler`同时也管理着由`createWorker`创建的`Worker`; Worker一般都是持有Schduler中的线程池, 提交的runnable也是提交到该线程池

### 2. subscribeOn

##### 2.1 Observable.subscribeOn

        public final Observable<T> subscribeOn(Scheduler scheduler) {
            ObjectHelper.requireNonNull(scheduler, "scheduler is null");
            return RxJavaPlugins.onAssembly(new ObservableSubscribeOn<T>(this, scheduler));
        }

    1. 参数检测
    2. 创建`ObservableOnSubscribe`对象, 并将当前Observable和Schduler传入;
    3. RxJavaPlugins的hook; 这个前面说过, 用于hook, 默认传入什么 就返回什么;

##### 2.2 ObservableSubscribeOn

1. `ObservableSubscribeOn`是Observable的子类, 内部包含一个`Observable`和`schduler`, 用于对原Obverable扩展, 是一个装饰模式; 
2. 上面说了, `ObservableSubscribeOn`是一个装饰模式, 继承于`HasUpstreamObservableSource`, 有一个`source`方法去获取被装饰的Observable对象;   
3. 上一篇说过, Observable.create方法创建的Observable, 实际是一个`ObservableCreater`对象, 现在ObservableScbscribeOn中包含的Observable即ObservableCreater;         
4. Observable的subscribe方法, 实际调用的是具体子类的`subscribeActual`方法;       

##### 2.3 ObservableSubscribeOn.subscribeActual
直接看 `ObservableSubscribeOn.subscribeActual` 的代码;     

        @Override
        public void subscribeActual(final Observer<? super T> s) {
            final SubscribeOnObserver<T> parent = new SubscribeOnObserver<T>(s);

            s.onSubscribe(parent);

            parent.setDisposable(scheduler.scheduleDirect(new SubscribeTask(parent)));
        }

1. 创建 `SubscribeOnObserver`, 并传入原始的Observer
2. 调用Observer的`onSubscribe`方法
3. 构建`SubscribeTask`, 并提交给Schduler去执行

##### 2.4 SubscribeOnObserver
`SubscribeOnObserver`是`ObservableSubscribeOn`的静态内部类, 同时也是继承于`Observer`, 内部也包含一个原始的`Observer`, 也是一个装饰模式;      

SubscribeOnObserver对被装饰类没有额外增加功能, 仅仅是一个封装, 在`onNext`, `onError`等方法中, 直接是调用的`actual.onNext`, `actual.onError`;       

##### 2.5 SubscribeTask
`SubscribeTask` 是一个runnable对象, 是ObservableSubscribeOn的内部类; 前面Schduler中说过, 切换线程, 就是将消息发送,包装成一个runnable, 提交给`Worker`去执行;      
这个`SubscribeTask`将原先的发送事件代码 封装成的runnable, 然后送去对应的线程池执行;     

直接看run方法 

        public void run() {
            source.subscribe(parent);
        }

`ObservableSubscribeOn`是Observable的子类, 同时是装饰模式, 内部持有一个Observable, source是被包装的Observable, 在此处的代码中, source即是`ObservableCreater`, parent是`SubscribeOnObserver`, source.subscribe即和第一篇中的逻辑一样了;    

##### 2.6 scheduler.scheduleDirect

    @NonNull
    public Disposable scheduleDirect(@NonNull Runnable run) {
        return scheduleDirect(run, 0L, TimeUnit.NANOSECONDS);
    }

    @NonNull
    public Disposable scheduleDirect(@NonNull Runnable run, long delay, @NonNull TimeUnit unit) {
        final Worker w = createWorker();

        final Runnable decoratedRun = RxJavaPlugins.onSchedule(run);

        DisposeTask task = new DisposeTask(decoratedRun, w);

        w.schedule(task, delay, unit);

        return task;
    }

1. 通过`createWorker`创建相应的`Worker`;
2. hook处理相应的runnable, 默认没处理;
3. 创建DisposeTask, 将需要运行的runnable对象, 封装成disposable对象, 用于执行取消操作;
4. 将封装后的runnable提交给worker去运行;

此处的scheduler由`Schedulers.single()`生成, 实际是一个`SingleScheduler`;   

###### 2.6.1  Worker.schedule()
直接看 `SingleScheduler`的代码

#######  Schedulers.createWorker 创建Worker; 获取公共的线程池, 创建Worker

        public Worker createWorker() {
            return new ScheduledWorker(executor.get());
        }

####### Worker.scheduler

        public Disposable schedule(@NonNull Runnable run, long delay, @NonNull TimeUnit unit) {
            if (disposed) {
                return EmptyDisposable.INSTANCE;
            }

            Runnable decoratedRun = RxJavaPlugins.onSchedule(run);

            ScheduledRunnable sr = new ScheduledRunnable(decoratedRun, tasks);
            tasks.add(sr);

            try {
                Future<?> f;
                if (delay <= 0L) {
                    f = executor.submit((Callable<Object>)sr);
                } else {
                    f = executor.schedule((Callable<Object>)sr, delay, unit);
                }

                sr.setFuture(f);
            } catch (RejectedExecutionException ex) {
                dispose();
                RxJavaPlugins.onError(ex);
                return EmptyDisposable.INSTANCE;
            }

            return sr;
        }

1. 封装传入的`runnable`对象, 将其封装成`ScheduledRunnable`对象
2. 提交给线程池运行, `ScheduledRunnable`本身是一个`Callable`对象, 可以用于取消执行

上述提交给线程池运行的流程, 最终封装的运行的`run`方法, 其实还是最先封装的`SubscribeTask`中的` source.subscribe(parent);`这一句代码;       
`SubscribeTask`本身对应的`runnable`被一次次传递封装, 最后给线程池运行;        
`source.subscribe(parent);`中, 上面说到是一个装饰模式, 运行的还是`Observable.subscribeActual`方法, 最后的运行逻辑和上一篇相同;     
最后会调到`ObservableSubscribeOn.onNext`方法, 内部没做处理, 装饰模式,调用上一级的`onNext`方法




