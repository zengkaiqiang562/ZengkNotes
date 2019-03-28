**概念**
RxJava 是一个基于事件流、实现异步操作的库。（类似于Android中的 AsyncTask 、Handler作用）

	RxJava：a library for composing asynchronous and event-based programs using observable 
	sequences for the Java VM
	// 翻译：RxJava 是一个在 Java VM 上使用可观测的序列来组成异步的、基于事件的程序的库

**特点**
由于 RxJava的使用方式是：基于事件流的链式调用，所以使得 RxJava：

    逻辑简洁
    实现优雅
    使用简单

更重要的是，随着程序逻辑的复杂性提高，它依然能够保持简洁，优雅

**原理**
Rxjava原理 基于一种扩展的观察者模式

Rxjava的扩展观察者模式中有4个角色：

	被观察者（Observable） 	发布事件 	
	观察者 （Observer） 	    接收事件，并给出响应动作 	
	订阅   （Subscribe） 	连接 被观察者与观察者 
	事件   （Event）         被观察者与观察者沟通的载体

被观察者（Observable）通过订阅（Subscribe）按顺序发送事件给观察者（Observer）， 观察者（Observer） 按顺序接收事件并作出对应的响应动作


## RxJava基本使用 ##
Rxjava的使用方式有两种：

    分步骤实现（该方法主要为了深入说明Rxjava的原理和使用方式，主要用于演示说明）

    基于事件流的链式调用（主要用于实际使用）

### 分步骤实现 ###

**step1. 创建被观察者（Observable）并发布事件。**

方式一：通过Observable的create方法发布事件：

	// 1. 创建被观察者 Observable 对象
    Observable<Integer> observable = 
			Observable.create(new ObservableOnSubscribe<Integer>() {

        // create() 是 RxJava 最基本的创造事件序列的方法
        // 此处传入了一个 ObservableOnSubscribe 对象参数
        // 当 Observable 被订阅时，ObservableOnSubscribe 的 subscribe 方法会自动被调用，
			即事件序列就会依照设定依次被触发
        // 即观察者会依次调用对应事件的重写方法从而响应事件
        // 从而实现被观察者调用了观察者的回调方法 & 由被观察者向观察者的事件传递，即观察者模式

        // 2. 在重写的subscribe（）里定义需要发送的事件
        @Override
        public void subscribe(ObservableEmitter<Integer> emitter) throws Exception {
            // 通过 ObservableEmitter类对象发布事件并通知观察者
            // ObservableEmitter类介绍
            // a. 定义：事件发射器
            // b. 作用：定义需要发送的事件并向观察者发送事件
            emitter.onNext(1);
            emitter.onNext(2);
            emitter.onNext(3);
            emitter.onComplete();
        }
    });


发布事件的类型有三类：Next事件、Complete事件、Error事件。

Next事件： 普通事件，向观察者发送需要响应事件的信号。
被观察者可发送无限个Next事件，观察者可接收无限个Next事件。
通过onNext方法发布Next事件。

Complete事件： RxJava把所有事件当作队列处理。Complete事件就是事件队列完结的事件。
标志着被观察者不再发送普通事件（Next事件）。
当被观察者发送一个Complete事件后，被观察者在Complete事件后的事件将会继续发送，但观察者收到Complete
事件后将不再继续接收任何事件。
被观察者可以不发送Complete事件。
Complete事件通过onComplete方法发送。
在一个正确运行的事件中，Complete事件只能发送一次，且当发送了Complete事件时，Error事件不能发送

Error事件： 事件队列异常事件。标志事件处理过程中出现了异常。
此时队列自动终止，不允许再有事件发出。
当被观察者发送一个Error事件后，被观察者在Error事件后的事件将会继续发送，但观察者收到Error事件后将不再
继续接收任何事件。
被观察者可以不发送Error事件。
在一个正确运行的事件中，Error事件只能发送一次，且当发送了Error事件时，Complete事件不能发送
通过onError()方法发送Error事件


**step2. 创建观察者（Observer）并定义响应事件的行为**
方式一：通过 Observer 接口

    // 创建观察者 （Observer ）对象
    Observer<Integer> observer = new Observer<Integer>() {

	    // 重写对应事件方法，实现响应对应事件
	
	    // 观察者接收事件前，默认最先调用 onSubscribe（）
	    @Override
	    public void onSubscribe(Disposable d) {
	        Log.d(TAG, "开始采用subscribe连接");
	    }
	
	    // 当被观察者发布Next事件并且观察者接收到时，会调用该方法进行响应
	    @Override
	    public void onNext(Integer value) {
	        Log.d(TAG, "对Next事件作出响应" + value);
	    }
	
	    // 当被观察者发布Error事件并且观察者接收到时，会调用该方法进行响应
	    @Override
	    public void onError(Throwable e) {
	        Log.d(TAG, "对Error事件作出响应");
	    }
	
	    // 当被观察者发布Complete事件并且观察者接收到时，会调用该方法进行响应
	    @Override
	    public void onComplete() {
	        Log.d(TAG, "对Complete事件作出响应");
	    }
	}；

方式二：通过 Subscriber 抽象类（在rxjava 1.2.1中才有此接口，在rxjava 2.0.1 版本中被移除了）

    //public abstract class Subscriber<T> implements Observer<T>, Subscription

    // 1. 创建观察者 （Subscriber ）对象
    Subscriber<String> subscriber = new Subscriber<Integer>() {

        @Override
        public void onSubscribe(Subscription s) {
            Log.d(TAG, "开始采用subscribe连接");
        }

        @Override
        public void onNext(Integer value) {
            Log.d(TAG, "对Next事件作出响应" + value);
        }

        @Override
        public void onError(Throwable e) {
            Log.d(TAG, "对Error事件作出响应");
        }

        @Override
        public void onComplete() {
            Log.d(TAG, "对Complete事件作出响应");
        }
    };

Subscriber抽象类实现了Observer接口，并且还实现了Subscription接口，
Subscriber抽象类提供如下方法：
1. onStart()：在还未响应事件前调用，用于做一些初始化工作 
2. unsubscribe()：用于取消订阅。在该方法被调用后，观察者将不再接收并响应事件。调用该方法前，先使用 isUnsubscribed() 判断状态，确定被观察者Observable是否还持有观察者Subscriber的引用，
如果引用不能及时释放，就会出现内存泄露。

**step3. 通过订阅（Subscribe）连接观察者和被观察者**

	observable.subscribe(observer);

### 基于事件流的链式调用 ###
基于事件流的链式调用其实就是将“分步骤实现”中的3个步骤通过一条代码语句完成：

	public static <T> Observable<T> create(ObservableOnSubscribe<T> source)

	public final void subscribe(Observer<? super T> observer)

如上是Observable的两个方法，create方法中声明要发布的事件，并返回一个Observable对象；再调用返回的
Observable对象的subscribe方法发布事件，同时传入一个接收事件的Observer对象作为subscribe方法的参数，
即：

	Observable<Integer> observable = 
			Observable.create(new ObservableOnSubscribe<Integer>() {

        @Override
        public void subscribe(ObservableEmitter<Integer> emitter) throws Exception {
            // 通过 ObservableEmitter类对象发布事件并通知观察者
            // ObservableEmitter类介绍
            // a. 定义：事件发射器
            // b. 作用：定义需要发送的事件并向观察者发送事件
            emitter.onNext(1);
            emitter.onNext(2);
            emitter.onNext(3);
            emitter.onComplete();
        }
    }).subscribe(new Observer<Integer>() {

	    @Override
	    public void onSubscribe(Disposable d) {
	        Log.d(TAG, "开始采用subscribe连接");
	    }
	
	    @Override
	    public void onNext(Integer value) {
	        Log.d(TAG, "对Next事件作出响应" + value);
	    }
	
	    @Override
	    public void onError(Throwable e) {
	        Log.d(TAG, "对Error事件作出响应");
	    }
	
	    @Override
	    public void onComplete() {
	        Log.d(TAG, "对Complete事件作出响应");
	    }
	});

### 指定要订阅的事件类型 ###
Observable提供多个重载的subscribe方法，可以指定要订阅的事件类型

    public final Disposable subscribe() {}
    	// 表示观察者不对被观察者发送的事件作出任何响应（但被观察者还是可以继续发送事件）

    public final Disposable subscribe(Consumer<? super T> onNext) {}
    	// 表示观察者只对被观察者发送的Next事件作出响应

    public final Disposable subscribe(Consumer<? super T> onNext, 
			Consumer<? super Throwable> onError) {} 
    	// 表示观察者只对被观察者发送的Next事件和Error事件作出响应

    public final Disposable subscribe(Consumer<? super T> onNext, 
			Consumer<? super Throwable> onError, Action onComplete) {}
    	// 表示观察者只对被观察者发送的Next事件、Error事件和Complete事件作出响应

    public final Disposable subscribe(Consumer<? super T> onNext, 
			Consumer<? super Throwable> onError, Action onComplete, 
			Consumer<? super Disposable> onSubscribe) {}
    // 表示观察者只对被观察者发送的Next事件、Error事件 、Complete事件 和 onSubscribe事件作出响应

    public final void subscribe(Observer<? super T> observer) {}
    // 表示观察者对被观察者发送的任何事件都作出响应

前面的5个重载的subscribe方法，都是通过构建出一个LambdaObserver对象（继承了Observer），再调用
subscribe(Observer<? super T>)方法实现的。

### 采用 Disposable.dispose() 切断观察者与被观察者之间的连接 ###
即观察者无法继续接收被观察者的事件，但被观察者还是可以继续发送事件

    Observer<Integer> observer = new Observer<Integer>() {
        // 1. 定义Disposable类变量
        private Disposable mDisposable;

        @Override
        public void onSubscribe(Disposable d) {
            Log.d(TAG, "开始采用subscribe连接");
            // 2. 对Disposable类变量赋值
            mDisposable = d;
        }

        @Override
        public void onNext(Integer value) {
            if (value == 2) {
                // 设置在接收到第二个事件后切断观察者和被观察者的连接，此后，观察者无法收到后面
					发布的Next事件或Complete事件或Error事件
                mDisposable.dispose();
                Log.d(TAG, "已经切断了连接：" + mDisposable.isDisposed());
            }
        }

        @Override
        public void onError(Throwable e) {
            Log.d(TAG, "对Error事件作出响应");
        }

        @Override
        public void onComplete() {
            Log.d(TAG, "对Complete事件作出响应");
        }
    };

# RxJava 的操作符 #

## 创建操作符 ##
创建操作符就是创建被观察者（Observable）对象，并发布事件。
（创建操作符其实就是Observable类中的方法）

### 基本创建 ###

	public static <T> Observable<T> create(ObservableOnSubscribe<T> source)

### 快速创建 ###

	public static <T> Observable<T> just(T item)

	public static <T> Observable<T> just(T item1, T item2)
	...
	//其实就是调用fromArray(T... items)方法。参数有多少个，Next事件发布多少次，T代表事件内容
	public static <T> Observable<T> just(T item1, T item2, T item3, T item4, T item5, 
			T item6, T item7, T item8, T item9, T item10)

	//数组T的元素有多少个，Next事件发布多少次，数组元素代表事件内容
	public static <T> Observable<T> fromArray(T... items)

	//集合Collection接口继承自Iterable，所以List、Set、Map集合都是Iterable的实现类。
		Iterable中的元素有多少个，Next事件发布多少次，Iterable中的元素代表事件内容
	public static <T> Observable<T> fromIterable(Iterable<? extends T> source)

	public static <T> Observable<T> fromPublisher(Publisher<? extends T> publisher)

	//被观察者不发送任何事件
	public static <T> Observable<T> never()

	//被观察者只发送Complete事件
	public static <T> Observable<T> empty()

	//被观察者只发送Error事件
	public static <T> Observable<T> error(final Throwable exception) {
        ...
        return error(Functions.justCallable(exception));
    }

	public static <T> Observable<T> error(Callable<? extends Throwable> errorSupplier)

### 延迟创建 ###

	public static <T> Observable<T> defer(
		Callable<? extends ObservableSource<? extends T>> supplier)

	public static Observable<Long> timer(long delay, TimeUnit unit) {
        return timer(delay, unit, Schedulers.computation());
    }

	public static Observable<Long> timer(long delay, TimeUnit unit, Scheduler scheduler)

	public static Observable<Long> interval(long period, TimeUnit unit) {
        return interval(period, period, unit, Schedulers.computation());
    }

	public static Observable<Long> interval(long initialDelay, long period, TimeUnit unit) {
        return interval(initialDelay, period, unit, Schedulers.computation());
    }

	public static Observable<Long> interval(long period, TimeUnit unit, Scheduler scheduler)
	{
        return interval(period, period, unit, scheduler);
    }

	public static Observable<Long> interval(long initialDelay, 
											long period, 
											TimeUnit unit, 
											Scheduler scheduler)

	public static Observable<Long> intervalRange(long start, long count, 
			long initialDelay, long period, TimeUnit unit) {

        return intervalRange(start, count, 
			initialDelay, period, unit, Schedulers.computation());
    }

	public static Observable<Long> intervalRange(long start, long count, 
			long initialDelay, long period, TimeUnit unit, Scheduler scheduler)

	public static Observable<Integer> range(final int start, final int count)
	public static Observable<Long> rangeLong(long start, long count)

**延迟的创建操作符：defer**
作用： 直到有观察者（Observer）订阅时，才动态创建被观察者对象（Observable），并发布事件
应用场景： 动态创建被观察者对象（Observable），并获取最新的Observable对象数据

	public static <T> Observable<T> defer(
		Callable<? extends ObservableSource<? extends T>> supplier)

如上，Callable接口用于实现一个带返回值的线程执行体方法call()，call()方法返回的其实就是所谓“动态创建”
的被观察者对象（Observable implements ObservableSource）。也就是说当Callable的call()方法被调用
的时候，才会创建被观察者对象（Observable），并发布事件。所以，发布的事件内容是执行call()方法之前最近
更新的那次数据。
只有当执行Observable的subscribe方法订阅事件时，Callable的call()方法才会被调用。

		//原始数据
        Integer i = 10;

        Observable<Integer> observable = Observable.defer(new Callable<ObservableSource<? extends Integer>>() {
            @Override
            public ObservableSource<? extends Integer> call() throws Exception {
                return Observable.just(i); //此处创建被观察者并发布事件
            }
        });

		//更新数据
        i = 15;

        //此时，defer方法的参数Callable对象的call()方法才会被调用，于是开始创建被观察者并发布事件
        observable.subscribe(new Observer<Integer>() {

            @Override
            public void onSubscribe(Disposable d) {
                Log.d(TAG, "开始采用subscribe连接");
            }

            @Override
            public void onNext(Integer value) {
                Log.d(TAG, "接收到的整数是"+ value  ); //value值是更新后的15
            }

            @Override
            public void onError(Throwable e) {
                Log.d(TAG, "对Error事件作出响应");
            }

            @Override
            public void onComplete() {
                Log.d(TAG, "对Complete事件作出响应");
            }
        });

**延迟的创建操作符 timer**
timer方法用于延迟发布事件，并且不能指定发布的事件内容，默认发布Next事件和Complete事件，并且Next事件
只发布一次且事件内容数据是Long类型（默认是0L）。
延迟从调用Observable的subscribe方法开始计算，
接收事件的方法默认在一个新的子线程中执行。

	public static Observable<Long> timer(long delay, TimeUnit unit) {
        return timer(delay, unit, Schedulers.computation());
    }

如果需要自定义Scheduler线程调度器，可采用timer(long delay, TimeUnit unit, Scheduler scheduler)

**延迟的创建操作符 interval**

	public static Observable<Long> interval(long initialDelay, long period, TimeUnit unit)

在调用subcribe方法订阅事件后，第 1 次 Next事件在延迟 initialDelay 时间后发布，事件内容数据是 0L；
在订阅事件后，第 2 次 Next事件在延迟 initialDelay + period 时间后发布，事件内容数据是 1L；
在订阅事件后，第 3 次 Next事件在延迟 initialDelay + 2*period 时间后发布，事件内容数据是 2L；
在订阅事件后，第 n 次 Next事件在延迟 initialDelay + (n-1)*period 时间后发布，事件内容数据是 n-1 L

观察者（Observer）可以在接收到Next事件时，通过onSubscribe(Disposable d)中的参数d的dispose()方法
结束对Next事件的接收。但结束后Complete事件不会再发布。

采用默认的线程调度器Scheduler时，接收Next事件的方法运行在新的子线程中。

**延迟的创建操作符 intervalRange**
intervalRange方法创建的被观察者所发布的Next事件是有限的，由参数count决定。
参数start决定一次发布Next事件时的事件内容数据的值为start
当发布完count个Next事件时，被观察者还会发布Complete事件。
同样被观察者可以通过onSubscribe(Disposable d)中的参数d的dispose()方法来提前结束对Next事件的接收，
但提前结束后，Complete事件不会再接收

	public static Observable<Long> intervalRange(long start, long count, 
			long initialDelay, long period, TimeUnit unit)

采用默认的线程调度器Scheduler时，接收Next事件的方法运行在新的子线程中。

**延迟的创建操作符 range**
跟intervalRange类似，range方法创建的被观察者所发布的Next事件也是有限的，由参数count决定。
但是，被观察者不会延迟发布Next事件，且观察者接收事件的方法不会运行在一个新的子线程中

	public static Observable<Integer> range(final int start, final int count)



