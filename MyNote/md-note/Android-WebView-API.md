## android.webkit.WebView ##

	@Widget
	public class WebView extends AbsoluteLayout
			implements ViewTreeObserver.OnGlobalFocusChangeListener,
				ViewGroup.OnHierarchyChangeListener, ViewDebug.HierarchyHandler
	
	public void loadUrl(String url)

	public void loadUrl(String url, Map<String, String> additionalHttpHeaders)

	public void loadData(String data, String mimeType, String encoding)

	public void loadDataWithBaseURL(String baseUrl, String data,
            		String mimeType, String encoding, String historyUrl)

	public void postUrl(String url, byte[] postData) {
        checkThread();
        if (URLUtil.isNetworkUrl(url)) {
            mProvider.postUrl(url, postData);
        } else {
            mProvider.loadUrl(url);
        }
    }

	------------------
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

	----------------------
	public boolean canGoBack()

    public void goBack()

	public boolean canGoForward()

    public void goForward()

	public boolean canGoBackOrForward(int steps)
    
	//Steps is negative if backward and positive if forward
    public void goBackOrForward(int steps)

	public void clearCache(boolean includeDiskFiles)

	public void clearFormData()

	public void clearHistory()

	--------------------
	public WebSettings getSettings()

	public void setWebViewClient(WebViewClient client)

	public void setWebChromeClient(WebChromeClient client)

## android.webkit.WebSettings ##

	public abstract class WebSettings

	public abstract void setJavaScriptEnabled(boolean flag);

	public abstract void setCacheMode(@CacheMode int mode);
		//LOAD_DEFAULT|LOAD_CACHE_ELSE_NETWORK|LOAD_NO_CACHE|LOAD_CACHE_ONLY

	public abstract void setAppCacheEnabled(boolean flag);
	public abstract void setAppCachePath(String appCachePath);

	@Deprecated
    public abstract void setAppCacheMaxSize(long appCacheMaxSize);

## android.webkit.WebViewClient ##

	public class WebViewClient

## android.webkit.WebChromeClient ##

	public class WebChromeClient

