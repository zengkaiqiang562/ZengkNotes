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

MX自定义的插件类Share.java

	package com.minxing.kit.plugin.web.share;

	public class Share extends CordovaPlugin {
		...
		public boolean execute(String var1, JSONArray var2, 
			final CallbackContext var3) throws JSONException {
			...
		}
		...
	}


博客自定义的插件类 ToastDemo.java

	package fxp.cordova.plugins;

	public class ToastDemo extends CordovaPlugin {
	 
	    @Override
	    public boolean execute(String action, CordovaArgs args, 
				CallbackContext callbackContext) throws JSONException {
	 
	        if ("toast".equals(action)) {

	            String str = args.getString(0);
	            Toast.makeText(cordova.getActivity(), str, Toast.LENGTH_LONG).show();
	            return true;

	        } else if ("toastWithCallback".equals(action)) {

	            String str = args.getString(0);
	            Toast.makeText(cordova.getActivity(), str, Toast.LENGTH_LONG).show();

	            //为体现成功／失败两种回调，这里使用Random生成随机数，
				//随机数为true时回调成功function，为false时回调失败function，
	            Random random = new Random();
	            if (random.nextBoolean()) {
	                callbackContext.success("execute_callack_success");
	            } else {
	                callbackContext.error("execute_callack_error");
	            }
	            return true;
	        }
	        return super.execute(action, args, callbackContext);
	    }
	}


### step2 配置Cordova插件 （res/xml/config.xml） ###
config.xml文件会在CordovaActivity的onCreate方法中调用loadConfig()方法进行解析
loadConfig()方法通过ConfigXmlParser类来解析此config.xml文件

敏行SDK中将config.xml文件另外命名为
mx_web_config.xml（在SDK源码中） 和 mx_web_ext_config.xml(在client源码中)

在config.xml文件中通过<feature>标签来配置插件

	敏行sdk 举例：

	<feature name="Share" >
        <param
            name="android-package"
            value="com.minxing.kit.plugin.web.share.Share" />
        <param
            name="onload"
            value="true" />
    </feature>


	博客文章 举例：

    <!--ToastDemo插件-->
    <feature name="ToastDemo">
        <param name="android-package" value="fxp.cordova.plugins.ToastDemo" />
    </feature>

### step3 在cordova_plugins.js中注册Cordova插件 ###
敏行中除了定义了 cordova_plugins.js外，还有一个cordova_ext_plugin.js文件。
这两个文件都在cordova.js中调用到。

cordova_plugins.js文件的路径在MXSDK下的
	assets/appstore/plugin/core/www/cordova_plugins.js

cordova.js文件的路径在MXSDK下的
	assets/appstore/plugi/core/www/cordova.js

cordova_ext_plugins.js文件的路径在MXClient下的
	assets/appstore/plugin/core/www/cordova_ext_plugins.js

但一般情况下，是没有cordova_ext_plugins.js文件的

并且 cordova_plugins.js 文件 的路径为：
	Cordova工程名/platforms/android/app/src/main/assets/www/cordova_plugins.js

并且 cordova.js 文件 的路径为：
	Cordova工程名/platforms/android/app/src/main/assets/www/cordova.js

打开cordova_plugins.js文件（或cordova_ext_plugins.js文件），
在module.exports数组中添加自定义插件信息。

	敏行自定义插件举例（cordova_plugins.js文件）

	//在cordova_ext_plugins.js文件中，
	//cordova.define方法的第一个参数为'cordova/plugin_ext_list'

	cordova.define('cordova/plugin_list', function(require, exports, module) {
		module.exports = [
			{...}, 
			...,
			{
				//file 对应 一个 js文件
				//id 对应一个 自定义插件Java类的全路径名
				//clobbers 对应在 JavaScript语法中此 自定义插件 对应的JavaScript对象
				
		        "file": "plugins/com.minxing.kit.plugin.web.share/www/share.js",
		        "id": "com.minxing.kit.plugin.web.share.Share",
		        "clobbers": [
		            "MXShare"
		        ]
		    },
			...,
			{...},
		];

		module.exports.metadata = 
		{
		    "org.apache.cordova.contacts": "0.2.16",
		    "org.apache.cordova.console": "0.2.13",
		    "org.apache.cordova.device-motion": "0.2.11",
		    "org.apache.cordova.device-orientation": "0.3.11",
		    "org.apache.cordova.file": "1.3.3",
		    "org.apache.cordova.file-transfer": "0.5.0",
		    "org.apache.cordova.geolocation": "0.3.12",
		    "org.apache.cordova.globalization": "0.3.4",
		    "org.apache.cordova.inappbrowser": "0.6.0",
		    "org.apache.cordova.media": "0.2.16",
		    "org.apache.cordova.media-capture": "0.3.6",
		    "org.apache.cordova.network-information": "0.2.15",
		    "org.apache.cordova.splashscreen": "1.0.0",
		    "org.apache.cordova.vibration": "0.3.13",
		    "org.apache.cordova.battery-status": "0.2.12",
		    "org.apache.cordova.camera": "0.3.6",
		    "org.apache.cordova.device": "0.3.0",
		    "org.apache.cordova.dialogs": "0.3.0",
			"com.cordova.plugins.sms": "0.1.6",
		}
	});
	
	-----------------------------

	博客中自定义插件举例

	{
        "file": "plugins/fxp-cordova-plugins/www/ToastDemo.js", //js文件路径
        "id": "fxp-cordova-plugins.ToastDemo", //插件moduleid。对应cordova.define的第一个参数
        "clobbers": [
            "fxp"//js中调用时使用的对象名
        ]
    }

一个plugin插件可以有多个module（类似于Android Studio中project和module的概念），
这里的id是插件当前module的id，

"id"字段的命名规则为【插件id】.【feature的name值】。插件id应使用"pluginId"为key值。此示例中，"pluginId":"fxp-cordova-plugins"。


### step4 定义Cordova插件调用方式 ###
定义Cordova插件调用方式 就是 定义一个 js文件 来调用 自定义插件Java类中的execute方法

博客中
在 Cordova工程名/platforms/android/assets/www/plugins路径下创建自己的文件夹(文件夹名称可以
以此插件对应的Java类的包名来命名)，
并在自己的文件夹下创建www目录，然后在此www目录下创建定义插件使用方式的js文件：
Cordova工程名/platforms/android/assets/www/plugins/fxp-cordova-plugins/www/TestDemo.js

敏行中的js文件路径为：
assets/appstore/plugin/core/www/plugins/com.minxing.kit.plugin.web.share/www/share.js


TestDemo.js内容如下：

	/**
	* cordova.define 方法参数说明:
	* 参数1 就是cordova_plugins.js里面定义的id
	* 
	* cordova.exec 方法参数说明：
	* 参数1：成功回调function
	* 参数2：失败回调function
	* 参数3：feature name，与config.xml中注册的一致
	* 参数4：调用自定义插件Java类时的action
	* 参数5：要传递的参数，json数组格式
	* 
	* 下面提供三种实现方式，三种实现方式均可行
	*/
	cordova.define("fxp-cordova-plugins.ToastDemo",
	    function(require, exports, module) {
	 
	        /*
	         * 实现方式一
	        */
	        var exec = require("cordova/exec");
	        module.exports = {
	            toast: function(content){
	                exec(null,null,"ToastDemo","toast",[content]);
	            },
	            toastWithCallback: function (content, successCallback, errorCallback) {
	                cordova.exec(successCallback, errorCallback, "ToastDemo", "toastWithCallback", [content]);
	            }
	        }
	 
	        /*
	         * 实现方式二
	        */
	/*        var exec = require('cordova/exec');
	        var FXP = function(){};
	        FXP.prototype.toast=function(content) {
	            exec(null, null, "ToastDemo", "toast", [content]);
	        };
	        FXP.prototype.toastWithCallback=function(content,success, error) {
	            exec(success, error, "ToastDemo", "toastWithCallback", [content]);
	        };
	        var fxp = new FXP();
	        module.exports = fxp;*/
	 
	        /*
	         * 实现方式三
	        */
	/*        var exec = require('cordova/exec');
	        exports.toast = function(content) {
	            exec(null, null, "ToastDemo", "toast", [content]);
	        };
	        exports.toastWithCallback=function(content, successCallback, errorCallback){
	            exec(successCallback,errorCallback,"ToastDemo","toastWithCallback",[content]);
	        };*/
	});


### step5 调用Cordova插件 ###
调用Cordova插件 是指在 js文件中 间接调用执行到 自定义插件Java类中的 execute 方法

在js文件中的调用方式为：

	[clobbers].[action]

以博客为例：

        fxp.toast("toast");
 
        fxp.toastWithCallback("toastWithCallback",

			function(success){
	            alert(success);
	        },

			function(error){
            	alert(error);
			}
		);
