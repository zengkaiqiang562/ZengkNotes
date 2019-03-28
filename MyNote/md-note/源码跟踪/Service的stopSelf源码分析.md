## Service 的stopSelf源码分析 ##

Service.java

	public final void stopSelf() {
        stopSelf(-1);
    }

    public final void stopSelf(int startId) {
        if (mActivityManager == null) {
            return;
        }
        try {
            mActivityManager.stopServiceToken(
                    new ComponentName(this, mClassName), mToken, startId);
        } catch (RemoteException ex) {
        }
    }

ActivityManagerService.java

	@Override
    public boolean stopServiceToken(ComponentName className, IBinder token,
            int startId) {
        synchronized(this) {
			//mServices是一个ActiveServices对象
            return mServices.stopServiceTokenLocked(className, token, startId);
        }
    }

ActiveServices.java

	boolean stopServiceTokenLocked(ComponentName className, IBinder token,
            int startId) {
        ...
        ServiceRecord r = findServiceLocked(className, token, 
			UserHandle.getCallingUserId());

        if (r != null) {
            if (startId >= 0) {
                ...
				//当参数startId不是最新的那个时，返回false，表示不能停止Service服务
                if (r.getLastStartId() != startId) {
                    return false;
                }
                ...
            }

			//真正停止Service服务
            synchronized (r.stats.getBatteryStats()) {
                r.stats.stopRunningLocked();
            }
            ...
            return true;
        }
        return false;
    }

一个Service服务类可能会通过Context的startService方法启动多次，但Service类的onCreate方法只会在
最开始执行一次，可以认为一个Service服务类，在一个进程中只有一个相关的Service实例对象。
虽然Service类的onCreate方法只是在最开始创建Service实例时执行一次，但每次调用startService方法
都会执行一次Service类中的
`int onStartCommand(Intent intent, @StartArgFlags int flags, int startId)`方法，参数startId
每次调用startService时都对应一个唯一的startId数值。

Service类提供了stopSelf(int startId)方法来停止调用此方法的Service对象所表示的服务。但是，从
ActiveServices的stopServiceTokenLocked方法源码上看，当调用stopSelf方法传递的startId参数值不等于
最后一次调用startService并执行onStartCommand时的startId参数值，那么就无法停止服务。特别的，当
stopSelf方法的参数startId为-1时，不会在stopServiceTokenLocked方法中判断startId是否是最近一次
启动服务时对应的startId值，所以Service的stopSelf()方法会立即停止服务。
而当stopSelf方法的参数startId是一个大于0的值时，那么只有在此startId等于最近一次启动服务所对应的
startId时，才能停止服务。