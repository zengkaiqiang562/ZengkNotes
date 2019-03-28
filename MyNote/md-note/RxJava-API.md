## io.reactivex.Observable<T> ##

	public abstract class Observable<T> implements ObservableSource<T>

	public static <T> Observable<T> create(ObservableOnSubscribe<T> source)

	public static <T> Observable<T> just(T item)

	public static <T> Observable<T> just(T item1, T item2)
	...
	//其实就是调用fromArray(T... items)方法
	public static <T> Observable<T> just(T item1, T item2, T item3, T item4, T item5, 
			T item6, T item7, T item8, T item9, T item10)

	public static <T> Observable<T> fromArray(T... items)

	public static <T> Observable<T> fromIterable(Iterable<? extends T> source)

	public static <T> Observable<T> fromPublisher(Publisher<? extends T> publisher)

	public static <T> Observable<T> never()

	public static <T> Observable<T> empty()

	public static <T> Observable<T> error(Callable<? extends Throwable> errorSupplier)

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

	---------------------------------------
	public final void subscribe(Observer<? super T> observer)

	public final Disposable subscribe() {
        return subscribe(Functions.emptyConsumer(), 
						Functions.ERROR_CONSUMER, 
						Functions.EMPTY_ACTION, 
						Functions.emptyConsumer());
    }

	public final Disposable subscribe(Consumer<? super T> onNext) {
        return subscribe(onNext, 
						Functions.ERROR_CONSUMER, 
						Functions.EMPTY_ACTION, 
						Functions.emptyConsumer());
    }

	public final Disposable subscribe(Consumer<? super T> onNext, 
			Consumer<? super Throwable> onError) {

        return subscribe(onNext, 
						onError, 
						Functions.EMPTY_ACTION, 
						Functions.emptyConsumer());
    }

	public final Disposable subscribe(Consumer<? super T> onNext, 
			Consumer<? super Throwable> onError, Action onComplete) {

        return subscribe(onNext, 
						onError, 
						onComplete, 
						Functions.emptyConsumer());
    }

	public final Disposable subscribe(Consumer<? super T> onNext, 
			Consumer<? super Throwable> onError,
            Action onComplete, 
			Consumer<? super Disposable> onSubscribe) {

        ...

        LambdaObserver<T> ls = new LambdaObserver<T>(onNext, onError, 
			onComplete, onSubscribe);

        subscribe(ls);

        return ls;
    }
	-----------------------------------------

## io.reactivex.ObservableOnSubscribe<T> ##

	public interface ObservableOnSubscribe<T>

	void subscribe(ObservableEmitter<T> e) throws Exception;

## io.reactivex.ObservableEmitter<T> ##

	public interface ObservableEmitter<T> extends Emitter<T>

## io.reactivex.Emitter<T> ##

	public interface Emitter<T>

	void onNext(T value);
    
    void onError(Throwable error);

    void onComplete();

-----

## io.reactivex.Observer<T> ##

	public interface Observer<T>

	void onSubscribe(Disposable d);

	void onNext(T value);

	void onError(Throwable e);

	void onComplete();

## io.reactivex.functions.Consumer<T> ##

	public interface Consumer<T>

	void accept(T t) throws Exception

## io.reactivex.internal.functions.Functions ##

	public final class Functions

	------------
	public static <T> Consumer<T> emptyConsumer() {
        return (Consumer<T>)EMPTY_CONSUMER;
    }

	static final Consumer<Object> EMPTY_CONSUMER = new Consumer<Object>() {
        @Override
        public void accept(Object v) { }

        @Override
        public String toString() {
            return "EmptyConsumer";
        }
    };
	-------------

	public static final Consumer<Throwable> ERROR_CONSUMER = new Consumer<Throwable>() {
        @Override
        public void accept(Throwable error) {
            RxJavaPlugins.onError(error);
        }
    };

	------------
	public static final Action EMPTY_ACTION = new Action() {
        @Override
        public void run() { }

        @Override
        public String toString() {
            return "EmptyAction";
        }
    };