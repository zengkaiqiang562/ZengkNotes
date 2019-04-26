## com.minxing.client.LoadingActivity ##

LoadingActivity.onCreate

MyRunnable.run()

MXKit.getInstance().prepareResource  
	//在登录之前会发送初始化数据库，并判断是否要更新H5应用。
	//上面的初始化步骤完成后，才会在MXKit.MXKitPrepareResourceListener监听器的回调方法onComplete中
	//调用LoadingActivity的loginMXKit()方法开始进入登录逻辑


LoadingActivity.loginMXKit()
	//1. 先判断进入LoadingActivity时Intent是否携带了允许SSO（单点登录）的标志。
		boolean isSSOLogin = getIntent().getBooleanExtra(LAUNCH_TYPE_SSO_LOGIN, false);
		（通常isSSOLogin为false）

	//2. 如果不是单点登录，则调用MXKit.loginMXKitWithToken(Context var1, MXKit.MXKitLoginListener var2)方法进行登录


static class LoginKitListener implements MXKitLoginListener

	@Override
	public void onSuccess() {
		weakActivity.get().launchApp();
		...//请求"/api/v1/mobile/update_info"，处理app是否需要升级的逻辑
	}

	@Override
	public void onFail(MXError error) {
		Intent intent = null;
		boolean showWelcome = ResourceUtil.getConfBoolean(weakActivity.get(),"client_show_welcome"); 
		if (PreferenceUtils.isAPPFTT(weakActivity.get())&& showWelcome) {
			intent = new Intent(weakActivity.get(), SystemNewsActivity.class); //进入Splash界面
		} else {
			if (PreferenceUtils.isAPPFTT(weakActivity.get())) {
				PreferenceUtils.saveAPPFTTStatus(weakActivity.get());
			}
			intent = new Intent(weakActivity.get(), LoginActivity.class);
		}
		weakActivity.get().startActivity(intent);
		BadgeUtil.resetBadgeCount(weakActivity.get());
		weakActivity.get().finish();
	}

launchApp()
	...
	intent = new Intent(this, ClientTabActivity.class);
	startActivity(intent);

## com.minxing.kit.MXKit ##

loginMXKitWithToken(Context var1, MXKit.MXKitLoginListener var2)  //此方法在LoadingActivity中调用
	//1. 先从preference_list数据表中查询是否保存有UserToken (preference_user_token字段)，有则说明之前登录过，直接登录即可
			不需要再进入LoginActivity界面输入账号，密码登录。

	//2. 没有查询到userToken，则MXKitLoginListener的onFail方法触发(进入LoginActivity界面输入账号，密码登录 )

	//3. 如果有UserToken，在直接登录之前，会调用MXKit.initComplete(final Context var1)做一些初始化工作，之后再通过执行
			MXKitLoginListener.onSuccess()方法，在此方法内调用LoadingActivity.launchApp()方法进入首页ClientTabActivity

loginMXKit(final Context var1, final String var2, final String var3, MXKit.MXKitLoginListener var4)  //此方法在LoginActivity中调用
	//var2表示登录用户名，var3表示登录密码

	(new m()).a(var1, var2, var3, MXKit.getInstance().getKitConfiguration().getGrantType(), var1x, var6);
		//com.minxing.kit.internal.core.service.m类的a(Context var1, String var2, String var3, String var4, String var5, n var6)用于
			请求"/oauth2/token"，此请求返回的json数据，能构建一个UserToken，完成登录
		//UserToken构建好之后，监听回调对象var6中的success方法被调用，执行MXKit.this.initComplete(this.context);方法做一些初始化工作，
			之后再通过执行var4对象的MXKitLoginListener.onSuccess()方法进入首页

## com.minxing.client.LoginActivity ##

login()
	MXKit.getInstance().loginMXKit(LoginActivity.this, usernameString, passwordString, 
										new LoginListener(LoginActivity.this,usernameString));


static class LoginListener implements MXKitLoginListener

	@Override
	public void onSuccess() {
		// 登陆成功后逻辑
		...
		weakActivity.get().launchApp();
		...//请求"/api/v1/mobile/update_info"，处理app是否需要升级的逻辑
	}

launchApp()
	...
	Intent intent = new Intent(this, ClientTabActivity.class);
	startActivity(intent);
	finish();

