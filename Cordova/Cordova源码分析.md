## org.apache.cordova.CordovaActivity ##

	public void loadUrl(String var1) {
        if (this.appView == null) {
            this.init();
        }

        this.keepRunning = this.preferences.getBoolean("KeepRunning", true);
        this.appView.loadUrlIntoView(var1, true);
    }

	public void init() {
        this.appView = this.makeWebView();
        this.createViews();
        if (!this.appView.isInitialized()) {
            this.appView.init(this.cordovaInterface, this.pluginEntries, this.preferences);
        }

        this.cordovaInterface.onCordovaInit(this.appView.getPluginManager());
        String var1 = this.preferences.getString("DefaultVolumeStream", "");
        if ("media".equals(var1.toLowerCase(Locale.ENGLISH))) {
            this.setVolumeControlStream(3);
        }

        this.initCache();
    }

	protected CordovaWebView makeWebView() {
        return new CordovaWebViewImpl(this.makeWebViewEngine());
    }

	protected CordovaWebViewEngine makeWebViewEngine() {
        return CordovaWebViewImpl.createEngine(this, this.preferences);
    }

## org.apache.cordova.CordovaWebViewImpl ##

	public static CordovaWebViewEngine createEngine(Context var0, 
			CordovaPreferences var1) {

        String var2 = var1.getString("webview", 
				SystemWebViewEngine.class.getCanonicalName());

        try {
            Class var3 = Class.forName(var2);

            Constructor var4 = var3.getConstructor(Context.class, 
				CordovaPreferences.class);

            return (CordovaWebViewEngine)var4.newInstance(var0, var1);

        } catch (Exception var5) {
            throw new RuntimeException("Failed to create webview. ", var5);
        }
    }