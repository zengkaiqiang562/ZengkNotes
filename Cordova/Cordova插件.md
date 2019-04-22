## Cordova插件 ##
Cordova插件就是提供一个用js调用Android原生API的接口

## 开发 Cordova插件 的步骤 ##
前提： 准备一个已添加Android平台的Cordova工程

1. 实现原生自定义插件类
2. 配置Cordova插件
3. 注册Cordova插件
4. 定义Cordova插件调用方式
5. 调用Cordova插件

### step1 实现原生自定义插件类 ###

自定义插件类需要继承 org.apache.cordova.CordovaPlugin，
重写execute方法，

execute方法不是运行在WebView接口主线程，而是运行在WebCore线程

	public class CordovaPlugin {

	    public CordovaWebView webView;
	    public CordovaInterface cordova;
	
		public boolean execute(String var1, String var2, CallbackContext var3) 
				throws JSONException {
	
	        JSONArray var4 = new JSONArray(var2);
	
	        return this.execute(var1, var4, var3);
	    }
	
	    public boolean execute(String var1, JSONArray var2, CallbackContext var3) 
				throws JSONException {
	
	        CordovaArgs var4 = new CordovaArgs(var2);
	
	        return this.execute(var1, var4, var3);
	    }
	
	    public boolean execute(String var1, CordovaArgs var2, CallbackContext var3) 
				throws JSONException {
	
	        return false;
	    }
	}

	
	--------------
	public interface CordovaInterface {

		Activity getActivity();

		ExecutorService getThreadPool();
	}

### step2 配置Cordova插件 （res/xml/config.xml） ###