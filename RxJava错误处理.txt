Rx中, 很容易处理异常, 整个链式调用过程中,如果异常不做处理, 最后都会交给`onError`;

**不要过渡的依赖`onError`;  `onError`应该是在数据实在是无法处理的情况下,才被调用; 因为`onError`一旦被调用, 即意味着整个事件流结束;**

在传统的Java中, 一般产生异常, 可以自己决定处理或者抛出, 在Rx中也类似, 可以自己决定在产生异常后, 如何处理;

## Catch
和传统Java中Catch类似, 获取异常,然后自行决定如何处理;

##### onErrorReturn
  `onErrorReturn `操作符作用:  当发生错误是, **发送一个默认值,然后结束数据流(即调用`onComplete`)**; 使用后,Subscribe的`onError`方法不会被调用,会正常的调用`onComplete`结束;

![onErrorReturn.png](http://upload-images.jianshu.io/upload_images/1638978-c3abc16c93b975f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### onErrorResumeNext
`onErrorResumeNext`操作符的作用:  当错误发生时, 用一个数据流代替当前数据流, 继续发送数据; 和上面一样,Subscribe的onError方法不会被调用

![onErrorResumeNext.png](http://upload-images.jianshu.io/upload_images/1638978-8c3742f064e80190.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### onExceptionResumeNext
`onExceptionResumeNext`类似, 唯一的区别就是`onExceptionResumeNext`捕获的是异常
> 如果抛出的Throwable不是一个Exception, 该操作符无法捕获

## Retry
如果发生了异常, 也可以使用`retry`重新订阅; 使用`retry`重新订阅数据流后, Observable会**从头重新发射数据**, 意味着可能会重复处理数据

![retry.png](http://upload-images.jianshu.io/upload_images/1638978-64fe8d5b60ff4748.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
retry有三个重载方法
- retry  无限重新订阅
- retry(long)  带有最大重试次数, 次数超过,则不再重试
- retry(Func)  带有一个判读函数, 如果返回true, 则重试; 返回false,则结束

##### retryWhen
`retryWhen`和`retry`类似, 不过接受一个函数, 该函数返回一个Observable, 由Observable发射的数据, 决定是否需要重新订阅
- 如果返回的Observable发射一个数据, 则重新订阅
- 如果返回的Observable发射一个错误, 则不会重试
> 返回的Observable发射的数据类型不重要; Observable只是用来判断是否需要重试

## 不结束当前数据流, 捕获(处理,忽略)异常
在平常编码中, 处理一序列的数据, 通常对其中的一个数据处理异常时, 我们通常会捕获忽略异常, 跳过该数据, 继续处理剩下的数据; 

上述的操作符中, 上面的操作符并不能实现该功能; `onErrorReturn`遇到异常时,会结束该数据留, 不会处理剩下的数据;  而`retry`会从头重新发射数据, 有重复数据;    
这种情况需要和`flatMap`一起搭配使用; `flatMap`可以把每项数据转换成为一个Observable,  然后就可以针对每个数据处理异常了

```java

	Random random = new Random();
	Observable<Integer> observable =
			Observable.create(o -> {
				o.onNext(random.nextInt());
				o.onNext(random.nextInt());
				o.onError(new Exception());
				o.onNext(random.nextInt());
			}).flatMap(o -> Observable.just(o)
					.map(Object::toString)
					.map(Integer::valueOf)
					.onErrorReturn(throwable -> -1)
			);
		
```