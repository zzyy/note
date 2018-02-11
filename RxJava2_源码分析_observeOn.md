observeOn 
------

        Observable.create((ObservableOnSubscribe<Integer>) e -> {
            System.out.println("observable : " + Thread.currentThread());
            e.onNext(1);
        })
                .subscribeOn(Schedulers.io())
                .observeOn(Schedulers.single())
                .subscribe(integer -> {
                    System.out.println(integer);
                    System.out.println("observer:  " + Thread.currentThread());
                });

通过`observeOn`可以指定接收事件,所在的线程

源码分析目的:
1. 生产和消费事件,是如何在不同线程运行的
2. 事件是如何从生产者 传递给消费者的

### 1. Observable.observeoOn

    public final Observable<T> observeOn(Scheduler scheduler) {
        return observeOn(scheduler, false, bufferSize());
    }

    public final Observable<T> observeOn(Scheduler scheduler, boolean delayError, int bufferSize) {
        ObjectHelper.requireNonNull(scheduler, "scheduler is null");
        ObjectHelper.verifyPositive(bufferSize, "bufferSize");
        return RxJavaPlugins.onAssembly(new ObservableObserveOn<T>(this, scheduler, delayError, bufferSize));
    }

1. 获取bufferSize(); 即保存事件的buffer的大小, 默认通过`Flowable.bufferSize()`获取, 默认值是128; 可以通过系统属性`rx2.buffer-size`修改大小;
2. 创建`ObservableObserveOn`; 
3. 对ObservableObserveOn的hook, 默认是没做任何hook处理

### 2. ObservableObserveOn
`ObservableObserveOn`也是一个`Observable`, 内部也持有一个Observer, 仍然是一个装饰模式;       

#### 2.1 ObservableObserveOn.subscribeActual
Observable的sunscribe方法, 最后执行的都是`subscribeActual`方法, 直接看`ObservableObserveOn.subscribeActual`的代码;     

        @Override
        protected void subscribeActual(Observer<? super T> observer) {
            // 1. TrampolineScheduler 是在当前线程执行的, 直接执行subscribe方法
            if (scheduler instanceof TrampolineScheduler) {
                source.subscribe(observer);
            } else {
                // 1. 根据Schduler创建Worker
                Scheduler.Worker w = scheduler.createWorker();
                // 2. 创建 ObserveOnObserver, 执行被装饰Observable的subscribe
                source.subscribe(new ObserveOnObserver<T>(observer, w, delayError, bufferSize));
            }
        }
    
1. 由schduler创建对应的Worker
2. 创建`ObserveOnObserver`
3. 执行当前Observable持有的Observable的subscribe方法; (此代码中, 执行的是`ObservableSubscribeOn.subscribe`)
4. `source.subscribe`的逻辑, 同第一篇一样,  最后是在当前线程, 直接调用的`ObserveOnObserver.onNext`方法

### 3. ObserveOnObserver
`ObservableObserveOn`也是Observer的子类, 同时包含一个Observer, 仍然是装饰模式;      
