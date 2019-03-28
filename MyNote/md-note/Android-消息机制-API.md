## android.os.MessageQueue ##

	public final class MessageQueue

	private final boolean mQuitAllowed;

	boolean enqueueMessage(Message msg, long when)

	Message next() //从MessageQueue中提取不到消息时，会阻塞

	void quit(boolean safe) {
        if (!mQuitAllowed) {
            throw new IllegalStateException("Main thread not allowed to quit.");
        }

        synchronized (this) {
            if (mQuitting) {
                return;
            }
            mQuitting = true;

            if (safe) {
                removeAllFutureMessagesLocked();
            } else {
                removeAllMessagesLocked();
            }

            // We can assume mPtr != 0 because mQuitting was previously false.
            nativeWake(mPtr);
        }
    }

## android.os.Looper ##

	public final class Looper

	
	static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<Looper>();

	final MessageQueue mQueue;

	public static void prepare() {
        prepare(true);
    }

	private static void prepare(boolean quitAllowed) {
        if (sThreadLocal.get() != null) {
            throw new RuntimeException("Only one Looper may be created per thread");
        }
        sThreadLocal.set(new Looper(quitAllowed));
    }

	private Looper(boolean quitAllowed) {
        mQueue = new MessageQueue(quitAllowed);
        mThread = Thread.currentThread();
    }

	public static @Nullable Looper myLooper() {
        return sThreadLocal.get();
    }

	public static @NonNull MessageQueue myQueue() {
        return myLooper().mQueue;
    }

	public @NonNull MessageQueue getQueue() {
        return mQueue;
    }

	public @NonNull Thread getThread() {
        return mThread;
    }

	public void quit() {
        mQueue.quit(false); //直接退出消息循环系统
    }

	public void quitSafely() {
        mQueue.quit(true); //把MessageQueue中的消息处理完后，才会退出。
    }

	public static void loop() {
        final Looper me = myLooper();
        if (me == null) {
            throw new RuntimeException("No Looper; Looper.prepare() wasn't called on 
					this thread.");
        }
        final MessageQueue queue = me.mQueue;
        ...
        for (;;) {
            Message msg = queue.next(); // might block
            if (msg == null) {
                // No message indicates that the message queue is quitting.
                return;
            }
            ...
            final long start = (slowDispatchThresholdMs == 0) ? 0 : SystemClock.uptimeMillis();
            final long end;
            try {
                msg.target.dispatchMessage(msg);
                end = (slowDispatchThresholdMs == 0) ? 0 : SystemClock.uptimeMillis();
            } finally {
                if (traceTag != 0) {
                    Trace.traceEnd(traceTag);
                }
            }
            ...
            msg.recycleUnchecked();
        }
    }

## android.os.Handler ##

	public class Handler

	--------创建Handler对象-----------
	public Handler() {
        this(null, false);
    }

	public Handler(Callback callback) {
        this(callback, false);
    }

	//@hide
	public Handler(Callback callback, boolean async) {
        ...
        mLooper = Looper.myLooper();
        if (mLooper == null) {
            throw new RuntimeException(
                "Can't create handler inside thread that has not called 
					Looper.prepare()");
        }

        mQueue = mLooper.mQueue;
        mCallback = callback;
        mAsynchronous = async;
    }

	------------------

	public Handler(Looper looper) {
        this(looper, null, false);
    }

	public Handler(Looper looper, Callback callback) {
        this(looper, callback, false);
    }

	//@hide
	public Handler(Looper looper, Callback callback, boolean async) {
        mLooper = looper;
        mQueue = looper.mQueue;
        mCallback = callback;
        mAsynchronous = async;
    }

	-----------创建消息------------
	public final Message obtainMessage()
    {
        return Message.obtain(this);
    }

	public final Message obtainMessage(int what)
    {
        return Message.obtain(this, what);
    }

	public final Message obtainMessage(int what, Object obj)
    {
        return Message.obtain(this, what, obj);
    }

	public final Message obtainMessage(int what, int arg1, int arg2)
    {
        return Message.obtain(this, what, arg1, arg2);
    }

	public final Message obtainMessage(int what, int arg1, int arg2, Object obj)
    {
        return Message.obtain(this, what, arg1, arg2, obj);
    }
	
	----------发送消息-------------

	public final boolean sendMessage(Message msg)
    {
        return sendMessageDelayed(msg, 0);
    }

	public final boolean sendMessageDelayed(Message msg, long delayMillis)
    {
        if (delayMillis < 0) {
            delayMillis = 0;
        }
        return sendMessageAtTime(msg, SystemClock.uptimeMillis() + delayMillis);
    }

	public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
        MessageQueue queue = mQueue;
        if (queue == null) {
            RuntimeException e = new RuntimeException(
                    this + " sendMessageAtTime() called with no mQueue");
            Log.w("Looper", e.getMessage(), e);
            return false;
        }
        return enqueueMessage(queue, msg, uptimeMillis);
    }

	public final boolean sendMessageAtFrontOfQueue(Message msg) {
        MessageQueue queue = mQueue;
        if (queue == null) {
            RuntimeException e = new RuntimeException(
                this + " sendMessageAtTime() called with no mQueue");
            Log.w("Looper", e.getMessage(), e);
            return false;
        }
        return enqueueMessage(queue, msg, 0); 
			//参数0可以理解为从MessageQueue中提取消息进行处理的大致时间，时间越小，越先处理。
    }

	private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
        msg.target = this;
        ...
        return queue.enqueueMessage(msg, uptimeMillis);
    }

	-------发送空消息----------

	public final boolean sendEmptyMessage(int what)
    {
        return sendEmptyMessageDelayed(what, 0);
    }

	public final boolean sendEmptyMessageDelayed(int what, long delayMillis) {
        Message msg = Message.obtain();
        msg.what = what;
        return sendMessageDelayed(msg, delayMillis);
    }

	public final boolean sendEmptyMessageAtTime(int what, long uptimeMillis) {
        Message msg = Message.obtain();
        msg.what = what;
        return sendMessageAtTime(msg, uptimeMillis);
    }

	---------post方法发送消息------------

	private static Message getPostMessage(Runnable r) {
        Message m = Message.obtain();
        m.callback = r;
        return m;
    }

	private static Message getPostMessage(Runnable r, Object token) {
        Message m = Message.obtain();
        m.obj = token;
        m.callback = r;
        return m;
    }

	public final boolean post(Runnable r)
    {
       return  sendMessageDelayed(getPostMessage(r), 0);
    }

	public final boolean postAtTime(Runnable r, long uptimeMillis)
    {
        return sendMessageAtTime(getPostMessage(r), uptimeMillis);
    }

	public final boolean postAtTime(Runnable r, Object token, long uptimeMillis)
    {
        return sendMessageAtTime(getPostMessage(r, token), uptimeMillis);
    }

	public final boolean postDelayed(Runnable r, long delayMillis)
    {
        return sendMessageDelayed(getPostMessage(r), delayMillis);
    }

	public final boolean postAtFrontOfQueue(Runnable r)
    {
        return sendMessageAtFrontOfQueue(getPostMessage(r));
    }

	---------------------

	public void dispatchMessage(Message msg) {
        if (msg.callback != null) {
            handleCallback(msg);
        } else {
            if (mCallback != null) {
                if (mCallback.handleMessage(msg)) {
                    return;
                }
            }
            handleMessage(msg);
        }
    }

	private static void handleCallback(Message message) {
        message.callback.run();
    }

	public void handleMessage(Message msg) {}

### android.os.Handler.Callback ###

	public interface Callback

	public boolean handleMessage(Message msg);

## android.os.Message ##

	public final class Message implements Parcelable

	public int what;
    
    public int arg1;
    
    public int arg2;
    
    public Object obj;
    
    public Messenger replyTo;

    /*package*/ long when;

    /*package*/ Bundle data;

    /*package*/ Handler target;

    /*package*/ Runnable callback;

    /*package*/ Message next;

	public Message() {} //建议使用obtain静态方法获取一个Message对象

	public static Message obtain() {
        synchronized (sPoolSync) {
            if (sPool != null) {
                Message m = sPool;
                sPool = m.next;
                m.next = null;
                m.flags = 0; // clear in-use flag
                sPoolSize--;
                return m;
            }
        }
        return new Message();
    }

	public static Message obtain(Handler h) {
        Message m = obtain();
        m.target = h;

        return m;
    }

	public static Message obtain(Handler h, Runnable callback) {
        Message m = obtain();
        m.target = h;
        m.callback = callback;

        return m;
    }

	public static Message obtain(Handler h, int what) {
        Message m = obtain();
        m.target = h;
        m.what = what;

        return m;
    }

	public static Message obtain(Handler h, int what, Object obj) {
        Message m = obtain();
        m.target = h;
        m.what = what;
        m.obj = obj;

        return m;
    }

	public static Message obtain(Handler h, int what, int arg1, int arg2) {
        Message m = obtain();
        m.target = h;
        m.what = what;
        m.arg1 = arg1;
        m.arg2 = arg2;

        return m;
    }

	public static Message obtain(Handler h, int what, int arg1, int arg2, Object obj) {
        Message m = obtain();
        m.target = h;
        m.what = what;
        m.arg1 = arg1;
        m.arg2 = arg2;
        m.obj = obj;

        return m;
    }

	public void sendToTarget() {
        target.sendMessage(this);
    }

	public void setTarget(Handler target) {
        this.target = target;
    }

	public Handler getTarget() {
        return target;
    }

	public Runnable getCallback() {
        return callback;
    }

	public void setData(Bundle data) {
        this.data = data;
    }

	public Bundle getData() {
        if (data == null) {
            data = new Bundle();
        }

        return data;
    }

	public Bundle peekData() {
        return data;
    }

	

	