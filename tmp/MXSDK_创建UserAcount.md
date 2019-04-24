## com.minxing.colorpicker.df ##

	public void a(UserAccount var1) {  //通过df.iA().a 调用
        df var2 = PY;
        synchronized(PY) {
            u.saveObj("user", var1);
            MXLog.log("mxdebug", "MXCacheManager save [cacheUser]user:" + var1);
            PZ = var1;
        }
    }


## com.minxing.kit.internal.core.service.m ##	

	private UserAccount d(Context var1, JSONObject var2) //此方法中调用了 df.iA().a 将 UserAccount 保存在 user.so文件中

调用 d 方法的地方：

	public void j(n var1)
		"/api/v1/users/current/networks"

	public void a(Context var1, String var2, String var3, String var4, String var5, n var6)
		"/oauth2/token"