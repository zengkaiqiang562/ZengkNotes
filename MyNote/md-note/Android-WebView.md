现在很多App里都内置了Web网页（Hyprid App），比如说很多电商平台，淘宝、京东、聚划算等等
那么这种该如何实现呢？其实这是Android里一个叫WebView的组件实现的。

WebView是一个基于webkit引擎、展现web页面的控件。
Android的Webview在低版本和高版本采用了不同的webkit版本内核，4.4后直接使用了Chrome。

WebView的作用

    显示和渲染Web页面

    直接使用html文件（网络上或本地assets中）作布局

    可和JavaScript交互调用

WebView使用介绍：
一般来说Webview可单独使用，可联合其子类一起使用，所以接下来，我会介绍：

    Webview自身的常见方法；

    Webview的最常用的子类
    （WebSettings类、WebViewClient类、WebChromeClient类)

    Android和Js的交互

### WebView的状态 ###

	public void onResume() {
        checkThread();
        mProvider.onResume();
    }
		//激活WebView为活跃状态，能正常执行网页的响应

	public void onPause() {
        checkThread();
        mProvider.onPause();
    }
		//当页面被失去焦点被切换到后台不可见状态，需要执行onPause
		//通过onPause动作通知内核暂停所有的动作，比如DOM的解析、plugin的执行、JavaScript执行。

	public void pauseTimers() {
        checkThread();
        mProvider.pauseTimers();
    }
		//当应用程序(存在webview)被切换到后台时，这个方法不仅仅针对当前的webview，
			而是全局的全应用程序的webview
		//它会暂停所有webview的layout，parsing，javascripttimer。降低CPU功耗。

	public void resumeTimers() {
        checkThread();
        mProvider.resumeTimers();
    }
		//恢复pauseTimers状态

	public void destroy() {
        checkThread();
        mProvider.destroy();
    }
		//销毁Webview 
		//在关闭了Activity时，如果Webview的音乐或视频，还在播放。就必须销毁Webview 
		//但是注意：webview调用destory时,webview仍绑定在Activity上 
		//这是由于自定义webview构建时传入了该Activity的context对象 
		//因此需要先从父容器中移除webview,然后再销毁webview:

### 关于前进 / 后退网页 ###

	//是否可以后退
	Webview.canGoBack() 

	//后退网页
	Webview.goBack()
	
	//是否可以前进                     
	Webview.canGoForward()

	//前进网页
	Webview.goForward()
	
	//以当前的index为起始点前进或者后退到历史记录中指定的steps
	//如果steps为负数则为后退，正数则为前进
	Webview.goBackOrForward(intsteps) 

### Back键控制网页后退 ###
在不做任何处理前提下，浏览网页时点击系统的“Back”键,整个 Browser 会调用 finish()而结束自身。
要做到：点击返回后，是网页回退而不是推出浏览器，
那么：在当前Activity中处理并消费掉该 Back 事件

	public boolean onKeyDown(int keyCode, KeyEvent event) {
	    if ((keyCode == KEYCODE_BACK) && mWebView.canGoBack()) { 
	        mWebView.goBack();
	        return true;
	    }
	    return super.onKeyDown(keyCode, event);
	}

###  清除缓存数据 ###

	//清除网页访问留下的缓存
	//由于内核缓存是全局的因此这个方法不仅仅针对webview而是针对整个应用程序.
	Webview.clearCache(true);
	
	//清除当前webview访问的历史记录
	//只会webview访问历史记录里的所有记录除了当前访问记录
	Webview.clearHistory()；
	
	//这个api仅仅清除自动完成填充的表单数据，并不会清除WebView存储到本地的数据
	Webview.clearFormData()；

## WebSettings ##
对WebView进行配置和管理

	//如果访问的页面中要与Javascript交互，则webview必须设置支持Javascript
	webSettings.setJavaScriptEnabled(true);  
	
	//支持插件
	webSettings.setPluginsEnabled(true); 
	
	//设置自适应屏幕，两者合用
	webSettings.setUseWideViewPort(true); //将图片调整到适合webview的大小 
	webSettings.setLoadWithOverviewMode(true); // 缩放至屏幕的大小
	
	//缩放操作
	webSettings.setSupportZoom(true); //支持缩放，默认为true。是下面那个的前提。
	webSettings.setBuiltInZoomControls(true); //设置内置的缩放控件。若为false，则该WebView不可缩放
	webSettings.setDisplayZoomControls(false); //隐藏原生的缩放控件
	
	//其他细节操作
	webSettings.setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK); //关闭webview中缓存 
	webSettings.setAllowFileAccess(true); //设置可以访问文件 
	webSettings.setJavaScriptCanOpenWindowsAutomatically(true); //支持通过JS打开新窗口 
	webSettings.setLoadsImagesAutomatically(true); //支持自动加载图片
	webSettings.setDefaultTextEncodingName("utf-8");//设置编码格式

### 设置WebView缓存 ###
当加载 html 页面时，WebView会在/data/data/包名目录下生成 database 与 cache 两个文件夹
请求的 URL记录保存在 WebViewCache.db，而 URL的内容是保存在 WebViewCache 文件夹下

是否启用缓存：

    //优先使用缓存: 
    WebView.getSettings().setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK); 
        //缓存模式如下：
        //LOAD_CACHE_ONLY: 不使用网络，只读取本地缓存数据
        //LOAD_DEFAULT: （默认）根据cache-control决定是否从网络上取数据。
        //LOAD_NO_CACHE: 不使用缓存，只从网络获取数据.
        //LOAD_CACHE_ELSE_NETWORK，只要本地有，无论是否过期，或者no-cache，都使用缓存中的数据。

    //不使用缓存: 
    WebView.getSettings().setCacheMode(WebSettings.LOAD_NO_CACHE);

结合使用（离线加载）：

	if (NetStatusUtil.isConnected(getApplicationContext())) {
	    webSettings.setCacheMode(WebSettings.LOAD_DEFAULT);//根据cache-control决定是否从网络上取数据。
	} else {
	    webSettings.setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK);//没网，则从本地获取，即离线加载
	}
	
	webSettings.setDomStorageEnabled(true); // 开启 DOM storage API 功能
	webSettings.setDatabaseEnabled(true);   //开启 database storage API 功能
	webSettings.setAppCacheEnabled(true);//开启 Application Caches 功能
	
	String cacheDirPath = getFilesDir().getAbsolutePath() + APP_CACAHE_DIRNAME;
	webSettings.setAppCachePath(cacheDirPath); //设置  Application Caches 缓存目录

每个 Application 只调用一次 WebSettings.setAppCachePath()，
WebSettings.setAppCacheMaxSize()
	

## WebViewClient ##
处理各种通知 & 请求事件

**shouldOverrideUrlLoading()**
打开网页时不调用系统浏览器， 而是在本WebView中显示；
在网页上的所有加载都经过这个方法,这个函数我们可以做很多操作。

	//步骤1. 定义Webview组件
	Webview webview = (WebView) findViewById(R.id.webView1);
	
	//步骤2. 选择加载方式
	//方式1. 加载一个网页：
	webView.loadUrl("http://www.google.com/");
	
	//方式2：加载apk包中的html页面
	webView.loadUrl("file:///android_asset/test.html");
	
	//方式3：加载手机本地的html页面
	webView.loadUrl("content://com.android.htmlfileprovider/sdcard/test.html");
	
	//步骤3. 复写shouldOverrideUrlLoading()方法，使得打开网页时不调用系统浏览器， 
			而是在本WebView中显示
	webView.setWebViewClient(new WebViewClient(){
		@Override
		public boolean shouldOverrideUrlLoading(WebView view, String url) {
			view.loadUrl(url);
			return true;
		}
	});

**onPageStarted()**
开始载入页面调用的，我们可以设定一个loading的页面，告诉用户程序在等待网络响应。

	webView.setWebViewClient(new WebViewClient(){
		@Override
		public void  onPageStarted(WebView view, String url, Bitmap favicon) {
			//设定加载开始的操作
		}
	});

**onPageFinished()**
在页面加载结束时调用。我们可以关闭loading 条，切换程序动作。

	webView.setWebViewClient(new WebViewClient(){
		@Override
		public void onPageFinished(WebView view, String url) {
			//设定加载结束的操作
		}
	});

**onLoadResource()**
在加载页面资源时会调用，每一个资源（比如图片）的加载都会调用一次。

	webView.setWebViewClient(new WebViewClient(){
		@Override
		public boolean onLoadResource(WebView view, String url) {
			//设定加载资源的操作
		}
	});
**onReceivedError()**
加载页面的服务器出现错误时（如404）调用

	//步骤1：写一个html文件（error_handle.html），用于出错时展示给用户看的提示页面
	//步骤2：将该html文件放置到代码根目录的assets文件夹下
	
	//步骤3：复写WebViewClient的onRecievedError方法
	//该方法传回了错误码，根据错误类型可以进行不同的错误分类处理
	webView.setWebViewClient(new WebViewClient(){
		@Override
		public void onReceivedError(WebView view, int errorCode, String description, 
			String failingUrl){

			switch(errorCode){
				case HttpStatus.SC_NOT_FOUND:
					view.loadUrl("file:///android_assets/error_handle.html");
					break;
			}
		}
	});

## WebChromeClient ##
辅助 WebView 处理 Javascript 的对话框,网站图标,网站标题等等。




	