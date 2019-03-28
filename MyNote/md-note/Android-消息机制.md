## 概述 ##
Handler是Android消息机制的上层接口，通过Handler可以轻松地将一个任务切换到Handler所在的线程中执行。

Android的消息机制主要是指Handler的运行机制，而Handler的运行需要MessageQueue和Looper的支持。所以，
介绍Android的消息机制主要就是介绍Handler、MessageQueue、Looper这三个主要的API，以及它们在一起
协同工作的过程。

MessageQueue是用来存储Message对象所表示的消息的一个容器。这个容器内部采用单链表的数据结构来存储
Message对象，并对外提供插入消息、提取消息的接口，并且提取消息后会把消息从MessageQueue中删除。

MessageQueue只是负责存储消息，而Looper则负责从MessageQueue中取出消息并处理它。
Looper会以无限循环的方式读取MessageQueue，一旦发现MessageQueue中存储着消息，就把它取出来并处理。

通过MessageQueue和Looper就能构建出一个消息循环处理系统，Handler的作用就是将Message消息发送到这个
消息循环处理系统中，并且代理Looper实现对消息的处理过程。

## 创建消息循环处理系统 ##
在一个线程中构建消息循环处理系统，只要有一个Looper对象和一个MessageQueue对象就够了。因为这个消息循环
处理系统是线程内部独有的，不能被其他线程共享，所以要保证在线程内部只能创建一个Looper对象和一个
MessageQueue对象，并且这两个对象不能被其它线程对象访问到。于是可以通过ThreadLocal对象来保存
Looper对象，而MessageQueue对象则可以在创建Looper对象时通过Looper对象中的一个MessageQueue类型的
实例变量所引用。

如下是Looper.java的部分源码：

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


如上，sThreadLocal是一个ThreadLocal<Looper>类型的类变量，可认为在同一个进程中它只会被初始化一次。
调用静态方法Looper.prepare(boolean)就可以创建一个Looper对象，并把这个Looper对象放入sThreadLocal
类变量中，于是在一个线程中通过sThreadLocal保存了一个Looper对象后，只有在同一个线程中才能从
sThreadLocal中取出来，也就是说只要sThreadLocal.get()不为null，说明在调用get方法的线程中，已经
存在了一个Looper对象，于是在这个线程中调用prepare()就不会再创建一个Looper对象了，即保证了一个线程
中只有一个Looper对象存在，并且也只有在同一个线程中才能获取到这个Looper对象。
而在执行Looper的构造方法初始化这个Looper对象时，也会创建一个MessageQueue对象，并通过Looper对象的
mQueue实例变量来引用这个MessageQueue对象，于是，在保证一个线程只有一个Looper对象的情况下，也就
保证了这个线程中只有一个MessageQueue对象。

## Android 消息机制的运作流程 ##
**MessageQueue的工作过程**
MessageQueue通过`boolean enqueueMessage(Message msg, long when)`方法
将一个消息插入MessageQueue。enqueueMessage的主要操作就是单链表的插入操作


通过`Message next()`方法从MessageQueue中取出一个消息。
next方法是一个无限循环的方法，如果MessageQueue中没有消息，那么next方法会一直阻塞。当有新消息到来时，
next方法会返回这条消息，并将此消息从MessageQueue的单链表结构中移除。

**Looper的工作过程**

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

之前介绍的调用Looper.prepare()方法只是在调用此方法的线程中构建出一个消息循环处理系统，但是此系统还
并没有开始运行，只有在调用了Looper.looper()方法后，该线程中的消息循环处理系统才真正开始运行。
looper方法中也是一个无限for循环，再for循环中，如果MessageQueue的next方法返回了一个消息，那么就会
调用Message.target.dispatchMessage方法去处理这个消息，target其实就是一个Handler对象，所以说，
Looper对消息的处理，是通过Handler代理完成的。
当MessageQueue中没有消息时，那么next方法就会阻塞，也就是说looper方法中的无限for循环的执行流程就会
卡在`Message msg = queue.next();`这行代码上，不会继续往下执行。直到后续有消息插入到MessageQueue
中，并在next方法中提取并返回。当然，对于通过Looper.prepare(true)构建的消息循环系统，是可以通过调用
Looper.quit或quitSafely方法退出这个消息循环系统的运行的。被标记为退出状态的MessageQueue，它的next
方法就会返回null，于是Looper.looper()方法中的无限for循环就会结束。
通过上面的分析可知，当一个运行中的消息循环系统不需要再处理消息时，应该执行Looper的quit或quitSafely
方法结束消息循环系统的运行，否则，在消息循环系统所在的线程中就会一直存在无限for循环。

**主线程的消息循环系统的构建**
因为一个进程中只存在一个主线程，所以Looper类专门为主线程提供了构建消息循环系统的API：

	private static Looper sMainLooper; 

	public static void prepareMainLooper() {
        prepare(false);
        synchronized (Looper.class) {
            if (sMainLooper != null) {
                throw new IllegalStateException("The main Looper has already been prepared.");
            }
            sMainLooper = myLooper();
        }
    }

	public static @Nullable Looper myLooper() {
        return sThreadLocal.get();
    }

	public static Looper getMainLooper() {
        synchronized (Looper.class) {
            return sMainLooper;
        }
    }

如上，sMainLooper是一个类变量，即在一个进程中，sMainLooper是唯一的，并且只会在一个进程中被初始化
一次。prepareMainLooper()方法一般会在主线程中调用，方法中的prepare(false)方法用于构建一个消息
循环系统，prepare方法的参数quitAllowed设为false，表示在主线程中构建的消息循环系统一旦运行后，
是不能通过调用Looper的quit或quitSafely方法主动退出的。
prepareMainLooper()方法中sMainLooper类变量会持有一个对主线程中的Looper对象的引用，又因为
sMainLooper在同一个进程中是唯一的，所以在不同的线程中调用prepareMainLooper方法时，sMainLooper
类变量都是同一个，即在不同线程中访问sMainLooper都是指向主线程中的Looper对象。
所以根据prepareMainLooper方法中的执行流程，一旦在主线程中调用了prepareMainLooper方法为sMainLooper
类变量做了一次赋值初始化后，那么不管在哪个线程中再次调用prepareMainLooper方法时，sMainLooper都不为
null，所以一旦在主线程中调用过prepareMainLooper方法为主线程构建了一个消息循环系统后，就不能再次
调用prepareMainLooper方法为任何线程构建消息循环系统了。
启动主线程的消息循环系统的方式也是通过调用Looper.loop()方法实现的。

**Handler的工作过程**
Handler的工作过程包括两个方面的内容：发送消息到MessageQueue，和代理Looper处理从MessageQueue中
提取出来的消息。

**创建Handler**
在使用Handler发送消息和处理消息之前，要先创建一个Handler对象，并且需要将此Handler对象和一个Looper
对象以及一个MessageQueue对象关联起来，从而构成一个完整的消息机制。因为Looper对象中已经持有了一个对
MessageQueue对象的引用，即Looper对象的实例变量mQueue。所以只要将一个Looper对象传递给Handler对象
即可。那么怎么获取一个Looper对象呢？这里有两种方式：
方式一：如果创建Handler对象的线程中已存在一个消息循环系统，那么在此线程中调用Loop.myLooper()方法
即可从Looper类中的sThreadLocal类变量中获取到当前线程中的Looper对象。此方式对应的Handler的构造方法
如下：

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

如上，如果已知创建Handler对象的线程中存在Looper对象，并且我们的目的就是在此线程中构建消息循环系统，
那么在Handler的构造方法中调用Looper.myLooper方法获取此线程内部的Looper对象，并通过Handler对象的
mLooper实例变量引用。

方式二：如果创建Handler对象的线程中不存在Looper对象，或者要使用另外一个线程中的Looper对象，那么
在创建Handler对象时，要向Handler的构造方法中传入一个Looper对象。此时，构建了消息循环系统的线程
就是调用了Looper.prepare()方法的线程，而不一定是创建了这个Handler对象的线程。

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

需要注意的是，在某个线程中手动调用了Looper.prepare()方法构建一个消息循环系统之后，还要在此线程中
调用Looper.loop()方法来启动消息循环系统，否则Handler对象就只能往MessageQueue中发送消息，而不能
触发对消息的处理。

**使用Handler发送消息**

	private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
        msg.target = this;
        ...
        return queue.enqueueMessage(msg, uptimeMillis);
    }

Handler对象提供了大量的方法将一个Message消息对象发送到MessageQueue中，如sendMessage系列方法，
sendEmptyMessage系列方法和post系列方法。这些方法最后都会调用到Handler对象的
`enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis)`方法，将消息插入到
MessageQueue中，参数queue就是此Handler对象的mQueue实例变量所引用的MessageQueue对象。
在将Message消息对象插入到MessageQueue之前，会让Message对象持有一个对当前Handler对象的引用target，
后续处理此消息时，其实就是通过这里为的target变量所引用的Handler对象实现的。
参数uptimeMillis可以理解为Message消息对象被处理时的大致时间（受之前消息在消息循环系统所在线程中的
处理时间的影响，可以会延迟），但Message消息对象在消息循环系统中都是按照各自插入时的uptimeMillis时间
的先后顺序进行处理的。

**使用Handler处理消息**
Looper.java

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

Handler.java

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

	public interface Callback {
        public boolean handleMessage(Message msg);
    }
    
    public void handleMessage(Message msg) {}

如上的Looper和Handler的部分源码所示，在某个线程中调用Looper.loop()方法后，就会在此线程中启动之前
构建完的消息循环系统后（如果此线程之前没有调用Looper的prepare方法构建消息循环系统，那么在此线程中
调用Looper.loop()方法就会产生异常），此线程中的消息循环系统启动后，就会调用MessageQueue的next()
方法提取之前插入进MessageQueue中的Message消息对象，如果next()方法返回了一个之前插入到
MessageQueue中的Message对象，那么在Looper.loop()方法中，就会通过此Message对象的target实例变量
所引用的那个Handler实例来处理这个Message对象（target实例变量通常是在Handler的enqueueMessage方法
中设置的），即调用Handler对象target的dispatchMessage(Message msg)方法处理Message对象。
需要注意的是，Handler对象的dispatchMessage方法是在运行消息循环系统的线程中执行的，这个线程不一定
就是创建Handler对象的线程，而是创建了Handler对象的mLooper实例变量所引用的Looper对象的线程。
在Handler对象的dispatchMessage方法中处理参数Message对象表示的消息的方式有三种：

方式一：如果之前使用Handler发送消息Message对象的时候，为此Message对象的Runnable类型的
callback实例变量设置了一个Runnable实例的引用。那么Handler对此Message消息对象的处理就是执行
此Message对象的callback实例变量所引用的Runnable对象的run()方法。需要说明的是，执行run()方法的线程
和运行消息循环系统的线程是同一个线程。
通常在调用Handler的post方法发送消息时，会传入一个Runnable对象作为post方法的参数，而消息Message对象
的callback实例变量就是引用了这个参数Runnable对象

方式二：如果没有为Message对象的callback实例变量设置一个对Runnable对象的引用，但是却为Handler对象
mCallback实例变量设置了一个对Handler.Callback接口的实现类对象的引用，那么就会调用这个
Handler.Callback接口的实现类对象mCallback的`boolean handleMessage(Message msg)`方法来处理消息。
此方式下，通常我们会自己创建一个Handler.Callback类型的实例变量，并为此实例变量引用一个重写了
Handler.Callback.handleMessage(Message message) 方法的Handler.Callback接口的实现类对象。
然后在调用Handler的构造方法创建Handler对象的时候，将这个Handler.Callback接口的实现类对象传入
Handler的构造方法中，通过Handler对象mCallback实例变量引用。

方式三：
如果Handler对象没有使用方式一和方式二来处理Message消息对象，或者使用了方式二来处理Message消息对象，
但是Handler中的mCallback对象的handleMessage方法返回了false，那么此Handler对象就会通过方式三来
处理Message消息对象。
此方式中，就是重写Handler类的`void handleMessage(Message msg)`方法来处理消息。
通常Handler对象就是采用此方式来处理消息的

**创建Message消息**
Message类提供了大量重载的obtain静态方法来获取一个Message对象。不建议使用Message类的构造方法创建
Message对象。

Handler提供的obtainMessage方法获取Message对象，其实就是直接调用Message的obtain静态方法实现的。

Message对象还提供了一个sendToTarget()方法将消息本身发送到MessageQueue中，调用此方法之前，要求
Message对象的target实例变量不为null。

另外Message对象除了提供obj实例变量携带消息数据，还可以通过setData(Bundle data)方法来携带消息数据。





