## MXAppCenterActivity ##
我的应用界面

	package com.minxing.kit.ui.appcenter;

	protected void onCreate(Bundle var1) {
        super.onCreate(var1);

        this.appCenterManager = MXUIEngine.getInstance().getAppCenterManager();

        this.currentUserInfo = df.iA().iB();
        this.currentNetId = this.currentUserInfo.getCurrentIdentity().getNetwork_id();
        this.categoryShow = kh.g(this, "mx_appcenter_category_show", false);

        this.appCenterManager.setAppCenterActivity(this);

        this.permissionRequest = new PermissionRequest(this);

        this.initView();
    }

	private void initView() {

        this.setContentView(layout.mx_tab_app_center);

        this.customPullLayout = (CustomPullLayout)this.findViewById(id.mx_app_center_content);

        this.appView = (MXAppCenterView)this.findViewById(id.app_center_view);

        this.categoryView = (MXAppCategoryView)this.findViewById(id.app_center_category_view); //此容器中包含多个MXAppCenterView，一般不用

		//标题栏，会被ClientTabActivity中initAppcenterHeaderView()内设置的自定义header view替代
		this.headerContainer = (RelativeLayout)this.findViewById(id.view_header); 

		//此容器控件中会放置banner轮播图
        this.appCenterHeaderLayout = (LinearLayout)this.findViewById(id.app_center_header_layout);

		this.initBannerShow();


		//下拉刷新的操作
		this.customPullLayout.setOnRefreshListener(new onRefreshListener() {
            public void refresh() {
                if (!MXAppCenterActivity.this.categoryShow) {

                    MXAppCenterActivity.this.appView.loadData((List)null, new OnAppCenterViewLoadCallBack() {
                        public void loadComplete() {
                            synchronized(MXAppCenterActivity.this.uniqueInstance) {
                                MXAppCenterActivity.this.customPullLayout.stopRefresh();
                            }
                        }
                    });

                } else {

                    MXAppCenterActivity.this.categoryView.loadData(new OnAppCenterViewLoadCallBack() {
                        public void loadComplete() {
                            synchronized(MXAppCenterActivity.this.uniqueInstance) {
                                MXAppCenterActivity.this.customPullLayout.stopRefresh();
                            }
                        }
                    });

                }

                MXAppCenterActivity.this.initBannerShow();

                if (MXAppCenterActivity.this.appCenterManager.getRefreshExtensionListener() != null) {
                    MXAppCenterActivity.this.appCenterManager.getRefreshExtensionListener().onExtensionRefresh();
                }

            }
        });

	}

	private void initBannerShow() {
        if (MXKit.getInstance().getKitConfiguration().isBannerShow()) {
            this.appCenterHeaderLayout.setVisibility(0);

            this.mxBannerViewPaper = new MXBannerViewPaper(this); //轮播图控件

            this.appCenterHeaderLayout.removeAllViews();
            this.appCenterHeaderLayout.addView(this.mxBannerViewPaper, -1, -1);
        } else {
            this.appCenterHeaderLayout.setVisibility(8);
        }

    }

	protected void onResume() {
		if (!this.categoryShow) {
            this.appCenterManager.setAppView(this.appView);
        } else {
            this.appCenterManager.setAppView(this.categoryView);
        }

		//bool为true则会从服务器地址 /api/v1/apps/added 中获取所有应用，为false则只从数据库 appstore_app_list 中查询 所有应用
		if (!this.categoryShow) {
            this.appView.loadData(bool); 
        } else {
            this.categoryView.loadData(bool);
        }

		if (!this.categoryShow) {
            this.appView.enableAppChangeMonitor(); //监听广播 "com.minxing.appstore.refresh"，收到时加载应用列表
            this.appView.enableAppNetworkMonitor(); //监听广播 "com.minxing.action.network.status"，收到时加载应用列表
        } else {
            this.categoryView.enableAppChangeMonitor();
            this.categoryView.enableAppNetworkMonitor();
        }
	}

## MXBannerViewPaper.java ##
我的应用界面中 显示轮播图的控件

	public class MXBannerViewPaper extends FrameLayout implements OnPageChangeListener


## MXAppCenterView ##
展示应用集合 List<AppInfo>

	package com.minxing.kit.ui.appcenter;

	public class MXAppCenterView extends LinearLayout

	private void init() {

        this.appCenterManager = MXUIEngine.getInstance().getAppCenterManager();

        this.content = LayoutInflater.from(this.mContext).inflate(layout.mx_app_center_view, (ViewGroup)null);

        this.gridView = (DynamicGridView)this.content.findViewById(id.mx_gridview);

		this.initAppList();

        this.appCenterService = new AppCenterService();

		this.adapter = new bt(this.mContext, (List)this.appCenterDataStack.peek(), this.columnNum);

        this.gridView.setAdapter(this.adapter);

		this.gridView.setOnItemClickListener(new OnItemClickListener() {

            public void onItemClick(AdapterView<?> var1, View var2, int var3, long var4) {
                AppInfo var6 = (AppInfo)((List)MXAppCenterView.this.appCenterDataStack.peek()).get(var3);

				if (var6.isAppCenterAddButton()) {//"更多"按钮
                    if (MXAppCenterView.this.assignCategory) {
                        MXAppCenterView.this.appCenterManager.addApp(MXAppCenterView.this.mContext, MXAppCenterView.this.currentCategoryId);
                    } else {
						//通常执行这里，跳转到AllAppsActivity界面（全部应用）
                        MXAppCenterView.this.appCenterManager.addApp(MXAppCenterView.this.mContext);
                    }
                } else if (var6.getType() == 0) {
                    Intent var12 = new Intent(MXAppCenterView.this.mContext, AppCenterCategoryActivity.class);
                    var12.putExtra("INTER_CATEGORY_PAGE", var6);
                    MXAppCenterView.this.mContext.startActivity(var12);
                } else {
                    try {
						//通常执行到这里
                        MXAppCenterView.this.appInstallOrUpdate(var6, var2, true, true);
                    } catch (Exception var13) {
                        var13.printStackTrace();
                        u.b(MXAppCenterView.this.mContext, MXAppCenterView.this.mContext.getResources().getString(string.mx_app_store_app_launch_fail), 0);
                    }
                }

			}
		});

	}

	//点击我的应用中 某个应用，执行此方法，进入相应界面
	private void appInstallOrUpdate(AppInfo var1, View var2, boolean var3, boolean var4) {
        if (var2 != null) {
            if (!AppcenterLaunchHelper.getInstance().loadingAppContain(var1.getApp_id())) {
                TextView var5 = (TextView)var2.findViewById(id.app_upgrade_progress);
                String var6 = var1.getProgressText();
                if (TextUtils.isEmpty(var6) || !var6.contains("%")) {
                    this.loading.setVisibility(0);
                    ImageView var7 = (ImageView)var2.findViewById(id.app_new_flag);
                    ImageView var8 = (ImageView)var2.findViewById(id.app_need_install_flag);
                    AppcenterLaunchHelper.getInstance().setAppNeedInstallMarkView(var8);
                    AppcenterLaunchHelper.getInstance().setAppNewMarkView(var7);
                    AppcenterLaunchHelper.getInstance().setLoadingView(this.loading);
                    AppcenterLaunchHelper.getInstance().setAdapter(this.adapter);

                    AppcenterLaunchHelper.getInstance().launch(this.mContext, var1, var3, var4); 
                }
            }
        }
    }


## AppcenterLaunchHelper.java ##
启动 我的应用界面 中的 某个应用

	package com.minxing.kit.ui.appcenter.internal;

	public void launch(Context var1, AppInfo var2, boolean var3, boolean var4) {
        Log.i("AppcenterLaunchHelper", "[launch]context:" + var1);
        if (var2.getType() != 2) {
            this.loadingAppIds.add(var2.getApp_id());
        }

        AppCenterController.getInstance().launch(var1, var2, this.getOnAPPLaunchListener(), this.getOnAPPUpgradeListener(var1, var2, var4), (String)null);
    }

## AppCenterController.java ##

	public AppLauncher getAppLauncher(AppInfo var1) {
        if (var1 == null) {
            return null;
        } else {
            Object var2;
            switch(var1.getType()) {
            case 1:
                var2 = new NativeAppLauncher();
                break;
            case 2: //文件助手、话题、审批 的 type 为 2
                var2 = new RemoteAppLauncher();
                break;
            case 3: // 运维（未安装） 的 type 为 3
                var2 = new PluginAppLauncher();
                break;
            default:
                var2 = new PluginAppLauncher();
            }

            return (AppLauncher)var2;
        }
    }

	public void launch(Context var1, AppInfo var2, OnAPPLaunchListener var3, OnAPPUpgradeListener var4, String var5) {
        this.launch(var1, var2, var3, var4, var5, false);
    }

	public void launch(final Context var1, final AppInfo var2, final OnAPPLaunchListener var3, final OnAPPUpgradeListener var4, 
			final String var5, boolean var6) {

        AppLauncher var7 = getInstance().getAppLauncher(var2);

        if (!var6) {//第一次启动时需要认证，认证后再启动一次
			//对应 文件助手、话题、审批， var7为 RemoteAppLauncher ; 对于运维（未安装）， var7 为PluginAppLauncher
            var7.launch(var1, var2, var3, var4, var5, new OnPasswordVerityListener() {
                public void onHandleVerity() {
                    AppcenterLaunchHelper.getInstance().setLoadingViewVisibility(8);
                    UserAccount var1x = df.iA().iB();
                    if (var1x != null && var1x.getCurrentIdentity() != null) {
                        if (var2.isAuth_enable()) {
                            if (PasswordEntryHelper.getInstance().getPwdEntryType(var1, var1x.getLogin_name()) != 0) {
                                PasswordEntryHelper.getInstance().showAppAuthPasswordEntry(var1, var1x.getLogin_name(), MXScreenlock.PWD_SCREEN_MODE_CANCEL_ONLY, -1, new AppAuthRequestListener() {
                                    public void onSuccess() {
										//认证通过再启动一次，这里才是真正的启动
                                        AppCenterController.this.launch(var1, var2, var3, var4, var5, true);
                                    }

                                    public void onFailure() {
                                    }
                                });
                                return;
                            }

                            AppCenterController.this.showEnterPasswordSettingDialog(var1);
                        } else {
                            AppCenterController.this.launch(var1, var2, var3, var4, var5, true);
                        }

                    }
                }
            });
        } else {
            var7.launch(var1, var2, var3, var4, var5, (OnPasswordVerityListener)null);
        }
        ...
    }

## RemoteAppLauncher.java ##

	public class RemoteAppLauncher implements AppLauncher

	public void launch(Context var1, AppInfo var2, OnAPPLaunchListener var3, OnAPPUpgradeListener var4, String var5, 
			OnPasswordVerityListener var6) {

        this.launchRemoteWebApp(var1, var2, var3, var6);
    }

	private void launchRemoteWebApp(Context var1, AppInfo var2, OnAPPLaunchListener var3, OnPasswordVerityListener var4) {

        if (var4 != null) { //点击我的应用界面的  文件助手、话题、审批 这些应用时，第一次启动var4 不等于null，
            var4.onHandleVerity(); //在此方法中会再次启动，第二次启动时 var4 等于null。（第一次启动需要认证）

        } else if (!TextUtils.isEmpty(var2.getUrl())) {

            if (var2.getUrl().startsWith("#")) { //文件助手的url为"#ocus/file_helper"， 话题的url为"#topics"，审批的url为

                this.launchInsideUrlApp(var1, var2);

            } else if (var2.getUrl().startsWith("launchApp://")) {

                MXUIEngine.getInstance().getAppCenterManager().launchAppByUrl(var1, var2.getUrl());

            } else { // 审批的url为"/jc/apps/sso_redirect?app_id=approval&url=%2Fmxapproval%2Fapps.html%3FwebOpenNewWindow%3Dtrue"

                Intent var5 = new Intent(var1, MXWebActivity.class);
                String var6 = var2.getUrl();
                if (!TextUtils.isEmpty(var6) && !var6.startsWith("http")) {
                    var6 = MXKit.getInstance().getKitConfiguration().getServerHost() + var6;
                }

                var5.putExtra("MXKIT_WEB_LAUNCH_URL", var6);
                if (var3 != null) {
                    var3.handleIntent(var5);
                }

                (new AppCenterService()).logAppLaunch(var1, var2.getOid());
                var1.startActivity(var5);
            }

        }
    }

	private void launchInsideUrlApp(Context var1, final AppInfo var2) {
        String var3 = var2.getUrl();
        String[] var4;
        if (var3.startsWith("#ocus")) {

			/* 调用this.startOcuConversation(Context var1, int var2, int var3)打开一个公众号的聊天窗口 ConversationActivity

				根据 "#ocus" 后的字符串 在本地的 ocuinfos.so 文件中，或从url地址"/api/v1/ocus/by_ocu_id/%s" 查询对应公众号的详细信息。
				如文件助手"#ocus/file_helper"，会根据"file_helper"查询；
			
				之后还需要查询此公众号的聊天记录信息，从conversation_list数据表查询，或从url地址"/api/v1/conversations/about_ocu/%d"查询

				再获取到聊天记录Conversation之后再跳转到聊天窗口 ConversationActivity
			/*
			

        } else if (var3.startsWith("#topics")) {
            var4 = var3.split("/");
            if (var4.length == 1) {
				//点击 我的应用界面的 话题 应用时，var3 为"#topics"，所以不带参数跳转到CircleTopicsListActivity 话题列表界面
                Intent var8 = new Intent(var1, CircleTopicsListActivity.class);
                var8.addFlags(67108864);
                var1.startActivity(var8);
            } else {
                int var9 = Integer.valueOf(var4[1]);
                Intent var10 = new Intent(var1, TopicsDetailActivity.class);
                var10.putExtra("topic_id", var9);
                var10.putExtra("topic_name", var1.getString(string.mx_toast_topic_detail));
                var10.addFlags(67108864);
                var1.startActivity(var10);
            }
        }

    }

