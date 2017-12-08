observeOn 
------

        Observable.create((ObservableOnSubscribe<Integer>) e -> {
            System.out.println("observable : " + Thread.currentThread());
            e.onNext(1);
        })
                .subscribeOn(Schedulers.io())
                .subscribe(integer -> {
                    System.out.println(integer);
                    System.out.println("observer:  " + Thread.currentThread());
                });

Rxjava默认是在当前线程生发送事件, `subscribeOn`可以切换Observable发送事件所在的线程;     
如果没有使用ObserveOn指定消费事件的线程, Observer将在Observable发送事件的的线程, 消费事件;