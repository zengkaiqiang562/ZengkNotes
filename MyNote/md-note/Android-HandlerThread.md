HandlerThread.java

	public class HandlerThread extends Thread

	public HandlerThread(String name) {
        super(name);
        mPriority = Process.THREAD_PRIORITY_DEFAULT;
    }

	public HandlerThread(String name, int priority) {
        super(name);
        mPriority = priority;
    }

	@Override
    public void run() {
        mTid = Process.myTid();
        Looper.prepare();
        synchronized (this) {
            mLooper = Looper.myLooper();
            notifyAll();
        }
        Process.setThreadPriority(mPriority);
        onLooperPrepared();
        Looper.loop();
        mTid = -1;
    }

	protected void onLooperPrepared() {} 

	public Looper getLooper() {
        if (!isAlive()) { //线程处于新建或死亡状态时返回false。调用线程的start()方法后为true
            return null;
        }
        
        // If the thread has been started, wait until the looper has been created.
        synchronized (this) {
            while (isAlive() && mLooper == null) {
                try {
                    wait();
                } catch (InterruptedException e) {
                }
            }
        }
        return mLooper;
    }

	public boolean quit() {
        Looper looper = getLooper();
        if (looper != null) {
            looper.quit();
            return true;
        }
        return false;
    }

	public boolean quitSafely() {
        Looper looper = getLooper();
        if (looper != null) {
            looper.quitSafely();
            return true;
        }
        return false;
    }

Process.java

	//@param priority A Linux priority level, from -20 for highest scheduling
    //priority to 19 for lowest scheduling priority.
    public static final native void setThreadPriority(int priority)
            throws IllegalArgumentException, SecurityException;

如上，HandlerThread用于在一个子线程中构建消息循环处理系统。
HandlerThread继承自Thread，当调用start()方法启动HandlerThread线程后，在线程执行体run()方法中，
会调用Looper.prepare()方法和Looper.loop()方法，在HandlerThread线程中构建并启动一个消息循环处理
系统。
于是，只要获取了HandlerThread线程内部的Looper对象，就能通过Handler类中带有Looper类型参数的构造
方法，创建一个在HandlerThread线程中处理消息的Handler对象。

HandlerThread的使用步骤如下：
1. 通过HandlerThread提供的构造方法创建一个HandlerThread线程对象。HandlerThread类提供了两个构造
方法，允许修改HandlerThread线程的线程名称，同时也允许修改HandlerThread线程的优先级。需要注意的是，
HandlerThread线程类通过Process.setThreadPriority(int priority)方法设置优先级，此方法的优先级
参数范围是[-20,19]，数值越小优先级越大。而Thread.setPriority(int newPriority)方法设置的优先级
数值则是数值越大优先级越大。默认情况下，HandlerThread线程通常Process.setThreadPriority方法设置
的优先级是Process.THREAD_PRIORITY_DEFAULT（等于0）

2. 调用HandlerThread线程的start()方法启动HandlerThread线程。启动此线程后，当线程执行体run()方法
被调用执行后，就会在run方法中构建并启动一个消息循环处理系统。如HandlerThread的run()方法源码所示，
只要没有调用Looper的quit或quitSafely方法结束消息循环系统的运行，那么由于Loop的loop()方法中存在着
无限for循环，所以HandlerThread的run()方法就会一直在执行，直到消息循环处理系统结束，loop方法内部退出
无限for循环后，run()方法才会执行完成。

3. 调用了HandlerThread线程的start()方法启动线程后，就可以调用HandlerThread对象的getLooper()方法
获取到HandlerThread线程中的mLooper对象。如果在某个线程中调用getLooper()方法之前，HandlerThread
线程中的mLooper对象还没有在run()方法中的完成赋值初始化，那么那个调用getLooper()方法的线程就会阻塞，
直到MLooper对象在run()方法中完成赋值初始化。

4. 获取到HandlerThread线程中的mLooper对象之后。就可以通过Handler类的带Looper类型参数的构造方法
创建Handler对象，并将HandlerThread线程中的mLooper对象传入Handler对象中。于是就可以使用创建好的
Handler对象将消息发送到HandlerThread线程中的消息循环系统内的MessageQueue中，并且在HandlerThread
线程中执行Handler对象的dispatchMessage方法处理Message消息对象。

5. 当不再需要通过HandlerThread线程中的消息循环处理系统来处理Message消息时，通过调用HandlerThread
的quit或quitSafely方法即可结束消息循环处理系统的运行，接着HandlerThread线程的run方法也会执行完成，
于是HandlerThread线程就会终止。