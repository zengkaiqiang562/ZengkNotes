AsyncTask适用于在子线程中执行任务，然后在主线程中展示任务的执行进度以及执行结果的场景。

AsyncTask中封装了FutureTask线程任务，线程池和Handler。

AsyncTask不适合进行特别耗时的后台任务，对于特别耗时的后台任务，建议使用线程池。

## 创建AsyncTask ##
AsyncTask是一个抽象类，所以需要自定义一个继承自AsyncTask的类，然后创建自定义类的实例对象。
自定义类时，重写如下方法：

	public abstract class AsyncTask<Params, Progress, Result>
		//自定义类在继承AsyncTask的时候，需要为Params，Progress，Result这三个泛型参数指定确定
			的类类型，Params表示执行线程任务时需要传入的参数的类型，通过AsyncTask的execute方法
			传入此类型参数，在doInBackground方法中执行线程任务时，使用此类型参数。
			Progress泛型表示在doInBackground方法中调用publishProgress方法更新进度时，
			publishProgress方法可以接受的进度值参数的类型，此类型的进度值在onPregressUpdate方法
			中使用。
			Result泛型表示执行完doInBackground方法中，返回的结果数据的类型，此类型的结果数据在
			onPostExecute方法中使用。
		//如果不需要使用到这三个泛型参数，那么用Void代替

	@MainThread
    protected void onPreExecute() {}
		//此方法在主线程中执行，并且是在调用线程池的execute方法执行线程任务之前执行，所以此方法
			是用来做一些准备工作的

	@WorkerThread
    protected abstract Result doInBackground(Params... params);
		//此方法在线程池内的子线程中执行，用来处理一个具体的线程任务，在此方法中调用publishProgress
			方法可更新任务执行的进度，调用publishProgress方法后会在主线程中回调onProgressUpdate
			方法。另外此方法需要返回一个任务执行完成后的结果数据对象，返回的结果数据对象会作为在
			主线程中的回调执行的onPostExecute方法的参数。

	@MainThread
    protected void onProgressUpdate(Progress... values) {}
		//此方法在主线程中执行，通过用于给UI显示参数进度值

	@MainThread
    protected void onPostExecute(Result result) {}
		//doInBackground方法在子线程中执行完成后，会在主线程中回调执行此方法，用于处理任务完成后的
			收尾工作。

	@MainThread
    protected void onCancelled() {}
		//当线程任务被取消后会执行此方法，如果线程任务被取消后，onPostExecute方法是不会被调用到的

## 通过AsyncTask执行线程任务 ##
一个AsyncTask对象只能执行一次线程任务。
AsyncTask对象通过execute方法或executeOnExecutor方法来执行线程任务

	@MainThread
    public final AsyncTask<Params, Progress, Result> execute(Params... params) {
        return executeOnExecutor(sDefaultExecutor, params);
    }

	@MainThread
    public final AsyncTask<Params, Progress, Result> executeOnExecutor(Executor exec,
            Params... params)

**使用 AsyncTask 时的注意事项**
- AsyncTask对象必须在主线程中创建
- AsyncTask对象的execute方法和executeOnExecutor方法必须在主线程中调用
- 一个AsyncTask对象只能执行一次线程任务，即一个AsyncTask对象的execute方法或executeOnExecutor方法
只能被开发者主动调用一次，否则会报异常。
- onPreExecute、doInBackground、onProgressUpdate、onPostExecute、onCancelled这些方法都只是
在AsyncTask内部被动回调的方法，开发者不能主动调用它们。
- 在同一个进程中的所有调用execute方法执行线程任务的各个AsyncTask对象是串行执行任务的，即同一进程，
同一时间，对于调用execute方法开始执行线程任务的所有AsyncTask对象的集合中，只有一个AsyncTask对象在
执行线程任务。

**调用了execute方法的所有AsyncTask对象在同一进程中串行执行任务的原理**

	public static final Executor THREAD_POOL_EXECUTOR;

	public static final Executor SERIAL_EXECUTOR = new SerialExecutor();

	private static volatile Executor sDefaultExecutor = SERIAL_EXECUTOR;

	private static class SerialExecutor implements Executor {
        final ArrayDeque<Runnable> mTasks = new ArrayDeque<Runnable>();
        Runnable mActive;

        public synchronized void execute(final Runnable r) {
            mTasks.offer(new Runnable() {
                public void run() {
                    try {
                        r.run();
                    } finally {
                        scheduleNext();
                    }
                }
            });
            if (mActive == null) {
                scheduleNext();
            }
        }

        protected synchronized void scheduleNext() {
            if ((mActive = mTasks.poll()) != null) {
                THREAD_POOL_EXECUTOR.execute(mActive);
            }
        }
    }

	@MainThread
    public final AsyncTask<Params, Progress, Result> execute(Params... params) {
        return executeOnExecutor(sDefaultExecutor, params);
    }

	@MainThread
    public final AsyncTask<Params, Progress, Result> executeOnExecutor(Executor exec,
            Params... params) {
        ...
        exec.execute(mFuture);

        return this;
    }

如上，AsyncTask类中存在两个线程池变量SERIAL_EXECUTOR和THREAD_POOL_EXECUTOR，并且这两个线程池是
类相关的，即所有AsyncTask对象使用的都是同一个SERIAL_EXECUTOR线程池实例，和同一个
THREAD_POOL_EXECUTOR线程池实例。
SERIAL_EXECUTOR线程池只是用来把AsyncTask对象中的线程任务插入到ArrayDeque队列中，并且初始化线程
任务的执行，而THREAD_POOL_EXECUTOR线程池才是用来真正执行线程任务的。
从源码中可以看到，在把AsyncTask中的线程任务插入到队列的时候，还创建了一个Runnable对象对线程任务做
了包装处理，这个新建的Runnable对象的使用目的就是保证在一个线程任务执行完成后，再从队列中取出另一个
AsyncTask对象的线程任务放入THREAD_POOL_EXECUTOR线程池中接着执行。直到队列中没有其他AsyncTask的
线程任务。于是就做到了多个AsyncTask对象的线程任务在同一个进程中串行执行的效果。

如果想让多个AsyncTask对象的线程任务在同一个进程中并发执行，那么直接调用AsyncTask对象的
`executeOnExecutor(Executor exec,Params... params)`方法，传入THREAD_POOL_EXECUTOR线程池或
其他线程池即可。



