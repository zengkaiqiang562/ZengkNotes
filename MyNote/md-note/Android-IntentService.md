
	public abstract class IntentService extends Service

	private volatile Looper mServiceLooper;
    private volatile ServiceHandler mServiceHandler;
    private String mName;

	public IntentService(String name) {
        super();
        mName = name;
    }

	@Override
    public void onCreate() {
        // TODO: It would be nice to have an option to hold a partial wakelock
        // during processing, and to have a static startService(Context, Intent)
        // method that would launch the service & hand off a wakelock.

        super.onCreate();
        HandlerThread thread = new HandlerThread("IntentService[" + mName + "]");
        thread.start();

        mServiceLooper = thread.getLooper();
        mServiceHandler = new ServiceHandler(mServiceLooper);
    }

	private final class ServiceHandler extends Handler {
        public ServiceHandler(Looper looper) {
            super(looper);
        }

        @Override
        public void handleMessage(Message msg) {
            onHandleIntent((Intent)msg.obj);
            stopSelf(msg.arg1);
        }
    }

	@WorkerThread
    protected abstract void onHandleIntent(@Nullable Intent intent);

	-------------------------

	@Override
    public int onStartCommand(@Nullable Intent intent, int flags, int startId) {
        onStart(intent, startId);
        return mRedelivery ? START_REDELIVER_INTENT : START_NOT_STICKY;
    }

	@Override
    public void onStart(@Nullable Intent intent, int startId) {
        Message msg = mServiceHandler.obtainMessage();
        msg.arg1 = startId;
        msg.obj = intent;
        mServiceHandler.sendMessage(msg);
    }

如上，IntentService是一个抽象类，继承自Service。并且IntentService中封装了一个HandlerThread和一个
Handler。
使用IntentService时，需要自定义一个类继承自IntentService
IntentService的作用就是将启动服务时携带了数据信息的Intent对象封装到Message消息对象中，并通过Handler
发送到HandlerThread线程中运行的消息循环处理系统中进行处理。

通过重写IntentService提供的onHandleIntent(Intent intent)方法实现对Intent中携带的数据信息的处理。
onHandlerIntent方法是在HandlerThread线程中执行的。

之所以要在一个运行中的Service服务中开启子线程去处理任务，是因为Service服务在系统中的优先级比一个
单纯的线程要高很多，将一个子线程放在Service服务中执行，不容易被系统杀死。

**IntentService中执行线程任务的过程**
在IntentService的onCreate方法中创建HandlerThread线程，并调用HandlerThread线程的start方法
启动线程；然后使用此线程中的Looper对象创建一个Handler对象mServiceHandler。

当执行到IntentService的onStartCommand方法时，回调IntentService的onStart方法，在此方法中将Intent
对象封装进一个Message消息对象中，并通过mServiceHandler发送到HandlerThread线程中的消息循环处理系统
中的MessageQueue中，当在HandlerThread线程中处理Message消息对象时，会在线程中执行mServiceHandler
的handleMessage方法，此方法中，先调用我们重写的IntentService中的onHandleIntent方法处理Message
消息中的Intent对象，在onHandleIntent方法处理执行完成后，调用Service的stopSelf(in startId)方法
试图停止服务，但是如果多次启动了IntentService服务来执行线程任务，并且startId并不是最后启动服务时的
startId，那么就无法停止服务。直到最后一次启动了IntentService服务执行到handleMessage方法中的
stopSelf方法时，才能停止服务。



