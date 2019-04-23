## org.apache.cordova.CordovaActivity ##

	public void onCreate(Bundle var1) {
        this.loadConfig();
		...
	}

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

	protected void loadConfig() {
        ConfigXmlParser var1 = new ConfigXmlParser();
        var1.parse(this);

        this.preferences = var1.getPreferences();

        this.preferences.setPreferencesBundle(this.getIntent().getExtras());
        this.preferences.copyIntoIntentExtras(this);
        this.launchUrl = var1.getLaunchUrl();
        this.pluginEntries = var1.getPluginEntries();
        Config.parser = var1;
    }

## org.apache.cordova.ConfigXmlParser ##
	
	//解析xml目录下的mx_web_config.xml文件 和 mx_web_ext_config.xml 文件
	public void parse(Context var1) {

        int var2 = var1.getResources().getIdentifier("mx_web_config", "xml", var1.getClass().getPackage().getName());

        int var3 = var1.getResources().getIdentifier("mx_web_ext_config", "xml", var1.getClass().getPackage().getName());

        if (var2 == 0) {
            var2 = var1.getResources().getIdentifier("mx_web_config", "xml", var1.getPackageName());
        }

        if (var3 == 0) {
            var3 = var1.getResources().getIdentifier("mx_web_ext_config", "xml", var1.getPackageName());
        }

        if (var2 != 0) {
            this.parse((XmlPullParser)var1.getResources().getXml(var2));
        }

        if (var3 != 0) {
            this.parse((XmlPullParser)var1.getResources().getXml(var3));
        }

        if (var2 == 0 && var3 == 0) {
            LOG.e(TAG, "res/xml/mx_web_config.xml is missing!");
        }
    }

	public void parse(XmlPullParser var1) {
        int var2 = -1;

        while(var2 != 1) {
            if (var2 == 2) {
                this.handleStartTag(var1);
            } else if (var2 == 3) {
                this.handleEndTag(var1);
            }

            try {
                var2 = var1.next();
            } catch (XmlPullParserException var4) {
                var4.printStackTrace();
            } catch (IOException var5) {
                var5.printStackTrace();
            }
        }

    }

	public void handleStartTag(XmlPullParser var1) {

        String var2 = var1.getName();

        if (var2.equals("feature")) {

            this.insideFeature = true;

			//获取 <feature>标签的 name 属性值
            this.service = var1.getAttributeValue((String)null, "name"); 

        } else if (this.insideFeature && var2.equals("param")) {
			//解析 <feature> 标签 下的 子标签 <param>

			//获取 <param> 标签的 name 属性值
            this.paramType = var1.getAttributeValue((String)null, "name");

            if (this.paramType.equals("service")) { //<param> 标签的name属性值为 service 

                this.service = var1.getAttributeValue((String)null, "value");

            } else if (!this.paramType.equals("package") && !this.paramType.equals("android-package")) {

                if (this.paramType.equals("onload")) { //<param> 标签的name属性值为 onload 

                    this.onload = "true".equals(var1.getAttributeValue((String)null, "value"));
                }

            } else {//执行到此处时 ， <param> 标签的name属性值 一般为 android-package 
                this.pluginClass = var1.getAttributeValue((String)null, "value");
            }

        } else {

            String var3;

            if (var2.equals("preference")) { 
				//解析 <preference> 标签，name属性可能是"loglevel", "Fullscreen", "ShowTitle", "webview"
				//当为"webview"时，对应的value值可能为SystemWebViewEngine的全路径名

                var3 = var1.getAttributeValue((String)null, "name").toLowerCase(Locale.ENGLISH);
                String var4 = var1.getAttributeValue((String)null, "value");

                this.prefs.set(var3, var4);

            } else if (var2.equals("content")) {
				//解析 <content> 标签，此标签的src属性值表示 launchUrl

                var3 = var1.getAttributeValue((String)null, "src");
                if (var3 != null) {
                    this.setStartUrl(var3);
                }
            }
        }
    }

	public void handleEndTag(XmlPullParser var1) {
        String var2 = var1.getName();
        if (var2.equals("feature")) { // 处理 feature 结束标签 </feature>

			//this.service对应 <feature> 标签的name属性， 
			//this.pluginClass对应子标签<param>的name属性为"android-package"时的value属性值
            this.pluginEntries.add(new PluginEntry(this.service, this.pluginClass, this.onload));

            this.service = "";
            this.pluginClass = "";
            this.insideFeature = false;
            this.onload = false;

        }

    }

	private void setStartUrl(String var1) {
        Pattern var2 = Pattern.compile("^[a-z-]+://");
        Matcher var3 = var2.matcher(var1);
        if (var3.find()) {
            this.launchUrl = var1;
        } else {
            if (var1.charAt(0) == '/') {
                var1 = var1.substring(1);
            }

            this.launchUrl = "file:///android_asset/www/" + var1;
        }

    }

	public CordovaPreferences getPreferences() {
        return this.prefs;
    }

    public ArrayList<PluginEntry> getPluginEntries() {
        return this.pluginEntries;
    }

	public String getLaunchUrl() {
        return this.launchUrl;
    }

## org.apache.cordova.CordovaWebViewImpl ##

	public CordovaWebViewImpl(CordovaWebViewEngine var1) {
        this.engine = var1;
    }

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

	public void loadUrlIntoView(final String var1, final boolean var2) {
        if (!var1.equals("about:blank") && !var1.startsWith("javascript:")) {
            var2 = var2 || this.loadedUrl == null;
            if (var2) {
                if (this.loadedUrl != null) {
                    this.appPlugin = null;
                    this.pluginManager.init();
                }

                this.loadedUrl = var1;
            }

            final int var3 = this.loadUrlTimeout;
            final int var4 = this.preferences.getInteger("LoadUrlTimeoutValue", 60000);
            final Runnable var5 = new Runnable() {
                public void run() {
                    CordovaWebViewImpl.this.stopLoading();
                    LOG.e("CordovaWebViewImpl", "CordovaWebView: TIMEOUT ERROR!");
                    JSONObject var1x = new JSONObject();

                    try {
                        var1x.put("errorCode", -6);
                        var1x.put("description", "The connection to the server was unsuccessful.");
                        var1x.put("url", var1);
                    } catch (JSONException var3) {
                        ;
                    }

                    CordovaWebViewImpl.this.pluginManager.postMessage("onReceivedError", var1x);
                }
            };
            final Runnable var6 = new Runnable() {
                public void run() {
                    try {
                        synchronized(this) {
                            this.wait((long)var4);
                        }
                    } catch (InterruptedException var4x) {
                        var4x.printStackTrace();
                    }

                    if (CordovaWebViewImpl.this.loadUrlTimeout == var3) {
                        CordovaWebViewImpl.this.cordova.getActivity().runOnUiThread(var5);
                    }

                }
            };
            this.cordova.getActivity().runOnUiThread(new Runnable() {
                public void run() {
                    if (var4 > 0) {
                        CordovaWebViewImpl.this.cordova.getThreadPool().execute(var6);
                    }

                    CordovaWebViewImpl.this.engine.loadUrl(var1, var2); //真正加载url的地方
                }
            });
        } else {
            this.engine.loadUrl(var1, false);
        }
    }