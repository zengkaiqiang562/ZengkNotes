##  ##

#### startScan方法（ 扫一扫） ####

	public void startScan(Activity var1) {
        NativeOperation var2 = new NativeOperation();
        var2.construct("native://showScan");

		//最终处理是跳转到CaptureActivity
        NativeOperationInvoker.getInstance().handleNativeInvoke(var1, var2, (NativeOperationInvokerListener)null);
    }