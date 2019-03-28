ThreadLocal.java

	public class ThreadLocal<T>

	public ThreadLocal() {}

	public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
    }

	void createMap(Thread t, T firstValue) {
        t.threadLocals = new ThreadLocalMap(this, firstValue);
    }

	public T get() {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
        return setInitialValue();
    }

	ThreadLocalMap getMap(Thread t) {
        return t.threadLocals;
    }

	private T setInitialValue() {
        T value = initialValue();
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
        return value;
    }

	protected T initialValue() {
        return null;
    }

	public void remove() {
         ThreadLocalMap m = getMap(Thread.currentThread());
         if (m != null)
             m.remove(this);
     }

从ThreadLocal的set方法中可以知道，ThreadLocal对象将数据value放置在当前线程中的一个ThreadLocalMap
类型的集合中，即Thread线程对象中的实例变量threadLocals表示的集合。当在不同线程中调用set方法将数据
存储在ThreadLocal对象中时，就相当于把数据存储在了不同线程对象中的threadLocals实例变量表示的集合中。

从ThreadLocal的get方法中可以知道，从一个ThreadLocal对象中取出数据，其实就是在当前线程对象的
threadLocals实例变量表示的集合中获取数据。如果之前没有在这个当前线程对象的threadLocals集合中
存储过数据的话，那么就会返回null。

于是得出结论，只有在调用了ThreadLocal对象的set方法存储过数据的线程中，才能从线程对象的threadLocals
集合中取出数据，如果在此线程中，没有调用过ThreadLocal的set方法存储过数据，那么从此线程对象的
threadLocals集合中就取不到数据。
在一个线程中通过ThreadLocal对象的set方法存储的数据，在另一个线程中通过同一个ThreadLocal对象的get方法
是获取不到的。
如果在不同的线程中调用同一个ThreadLocal对象的set方法存储了不同的数据对象，那么在不同的线程中调用同一个
ThreadLocal对象的get方法取出来的数据对象也是不同的。