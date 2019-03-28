## java.lang.Thread ##

	public class Thread implements Runnable

	public Thread()
	public Thread(String name)

	public Thread(Runnable target)
	public Thread(Runnable target, String name)

	public synchronized void start()

	public final String getName()

	public static native Thread currentThread();

	public final boolean isAlive()
		//当线程处于就绪、运行、阻塞三种状态时，返回tru；
		//当线程处于新建、死亡状态时，返回false

	public static void sleep(long millis) throws InterruptedException

	public final void join() throws InterruptedException

	public final void join(long millis) throws InterruptedException

	public final void setDaemon(boolean on)
	public final boolean isDaemon()

	public static native void yield();

	public final static int MIN_PRIORITY = 1;
    public final static int NORM_PRIORITY = 5;
    public final static int MAX_PRIORITY = 10;
	
	public final void setPriority(int newPriority)
	public final int getPriority()


## java.lang.Runnable ##

	@FunctionalInterface  //函数式接口，可以使用Lambda表达式创建
	public interface Runnable

	public abstract void run();

## java.util.concurrent.Callable<V> ##

	@FunctionalInterface
	public interface Callable<V>

	V call() throws Exception;

## java.util.concurrent.Future<V> ##

	public interface Future<V>

	V get() throws InterruptedException, ExecutionException;
		//返回Callable任务中call()方法的返回值。
			调用该方法将导致程序阻塞，必须等到子线程结束才会得到放回值		

	V get(long timeout, TimeUnit unit) throws InterruptedException, ExecutionException,
											  TimeoutException;
		//该方法最多阻塞timeout和unit指定的时间，
			如果经过指定时间后Callable任务依然没有返回值，将抛出TimeoutException异常

	boolean cancel(boolean mayInterruptIfRunning);
		//true表示如果当前线程正在执行，也要中断它。false则不中断正在执行的线程

	boolean isCancelled();

	boolean isDone();

## java.util.concurrent.RunnableFuture<V> ##

	public interface RunnableFuture<V> extends Runnable, Future<V>

	void run();

## java.util.concurrent.FutureTask<V> ##
	
	public class FutureTask<V> implements RunnableFuture<V>

	public FutureTask(Callable<V> callable)

## java.util.concurrent.Executors ##

	public class Executors

	public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }

	public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }

	public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }

	public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
        return new ScheduledThreadPoolExecutor(corePoolSize);
    }

	public static ScheduledExecutorService newSingleThreadScheduledExecutor() {
        return new DelegatedScheduledExecutorService
            (new ScheduledThreadPoolExecutor(1));
    }


## java.util.concurrent.Executor ##
	
	public interface Executor

	void execute(Runnable command);

## java.util.concurrent.ExecutorService ##

	public interface ExecutorService extends Executor

	<T> Future<T> submit(Callable<T> task);

	Future<?> submit(Runnable task);

	<T> Future<T> submit(Runnable task, T result);

	void shutdown();

	List<Runnable> shutdownNow();

	boolean isShutdown();

## java.util.concurrent.ScheduledExecutorService ##

	public interface ScheduledExecutorService extends ExecutorService

	public ScheduledFuture<?> schedule(Runnable command,
                                       long delay, TimeUnit unit);

	public <V> ScheduledFuture<V> schedule(Callable<V> callable,
                                           long delay, TimeUnit unit);

	public ScheduledFuture<?> scheduleAtFixedRate(Runnable command,
                                                  long initialDelay,
                                                  long period,
                                                  TimeUnit unit);

	public ScheduledFuture<?> scheduleWithFixedDelay(Runnable command,
                                                     long initialDelay,
                                                     long delay,
                                                     TimeUnit unit);

## java.util.concurrent.ScheduledFuture<V> ##

	public interface ScheduledFuture<V> extends Delayed, Future<V>

## java.util.concurrent.Delayed ##

	public interface Delayed extends Comparable<Delayed>

	long getDelay(TimeUnit unit);

## java.util.concurrent.AbstractExecutorService ##

	public abstract class AbstractExecutorService implements ExecutorService

## java.util.concurrent.ThreadPoolExecutor ##

	public class ThreadPoolExecutor extends AbstractExecutorService

	public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory)

	public void allowCoreThreadTimeOut(boolean value)

## java.util.concurrent.ScheduledThreadPoolExecutor ##

	public class ScheduledThreadPoolExecutor extends ThreadPoolExecutor
        implements ScheduledExecutorService

## java.util.concurrent.ThreadFactory ##

	public interface ThreadFactory

	Thread newThread(Runnable r);


----------------------------------
## java.lang.ThreadLocal<T> ##

	public class ThreadLocal<T> 

	public ThreadLocal()

	public T get()

	public void set(T value)

	public void remove()