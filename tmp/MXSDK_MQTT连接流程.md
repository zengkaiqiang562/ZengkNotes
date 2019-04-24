## com.minxing.kit.MXKit ##

	public void loginMXKitWithToken(Context var1, MXKit.MXKitLoginListener var2) {
        MXLog.log("mxpush", "[MXKit] [loginMXKitWithToken]");

		//调用com.minxing.kit.internal.core.service.m类的 public void j(n var1) 方法，
		//发送 "/api/v1/users/current/networks" 请求
		...
			this.initComplete(var1); 
		...
	}

	public void loginMXKit(final Context var1, final String var2, final String var3, MXKit.MXKitLoginListener var4) {
        MXLog.log("mxpush", "[MXKit] [loginMXKit]");

		//调用com.minxing.kit.internal.core.service.m类的 
		//public void a(Context var1, String var2, String var3, String var4, String var5, n var6) 方法，
		//发送 "/oauth2/token" 请求

		...
			this.initComplete(this.context);	
		...

	}

	public void initComplete(final Context var1) {
        MXLog.log("mxdebug", "MXKit [initComplete]");

		...
		MXKit.this.syncConversationMessagesFromLastSeen(context);
		...
		
	}

	public void syncConversationMessagesFromLastSeen(Context var1) {
		
		//refreshConversations方法会先请求 "/api/v1/conversations/history_messages" 刷新历史聊天记录
		//刷新结束后，调用 startPushService(this.mContext); 启动推送服务
		
        this.refreshConversations(var1, new n(var1) {
            public void success(Object var1) {
               
                MXKit.this.startPushService(this.mContext);
                
            }

            public void failure(MXError var1) {
                
                MXKit.this.startPushService(this.mContext);
                
            }
        }, true);
    }

	private void startPushService(final Context var1) {
        MXLog.log("mxpush", "[MXKit] [startPushService]");

		//判断 PushConnectService 服务是否处于运行状态
        boolean var2 = com.minxing.kit.internal.push.a.c(var1, PushConnectService.class);
        if (var2) {

            MXLog.log("mxpush", "[MXKit] [startPushService]isServiceExist : {}", var2);
            PushConnectService.t(var1);
            Timer var3 = new Timer(true);
            TimerTask var4 = new TimerTask() {
                public void run() {
                    boolean var1x = com.minxing.kit.internal.push.a.c(var1, PushConnectService.class);
                    if (var1x) {
                        PushConnectService.aJ(var1);
                    }

                    MXKit.create("com.minxing.kit.push.service.launch");
                    PushConnectService.l(var1, true);
                }
            };
            var3.schedule(var4, 500L);

        } else {//第一次进入App时执行到这里

			//调用native方法
			//"com.minxing.kit.push.service.launch"表示一个广播Action
			//对应的广播接收者为com.minxing.kit.internal.push.PushReceiver
            create("com.minxing.kit.push.service.launch"); 

            PushConnectService.l(var1, true);
        }
    }


## com.minxing.kit.internal.core.PushConnectService ##

	/*启动PushConnectService服务类*/
	public static synchronized void l(Context var0, boolean var1) {
        MXLog.log("mxpush", "[PC] [startService] resetCurrentClient is {}", var1);

        Intent var2 = new Intent(var0.getApplicationContext(), PushConnectService.class);

        var2.putExtra(ajM, var1); //ajM="MX_INTENT_MQTT_FORCE_CLOSE",  var1=true

        var2.setFlags(32);

        var0.startService(var2);
    }

	public void onCreate() {
        MXLog.i("PushConnectService", "[onCreate]");
        MXLog.log("mxpush", "[PC] [onCreate]pid:{}", Process.myPid());

		UserAccount var1 = (UserAccount)u.readObj("user");
		this.clientId = String.valueOf(var1.getPush_client_id());

		this.mHandler = new Handler() {

            public void handleMessage(Message var1) {

				MXLog.log("mxpush", "[PC][handleMessage]msg.what is {}", var1.what);

                if (var1.what == 1001) {
                    MXLog.log("mxpush", "[PC] >>>[Connected]");

                } else if (var1.what == 1004) {
					Integer var2 = (Integer)var1.obj;
                    MXLog.log("mxpush", "[PC] [Connect Failed]reasonCode is{}", var2);

					switch(var2) {
                        case 1:
                            PushConnectService.this.O(true);
                        	break;
                        case 3:
                            MXLog.log("mxpush", "[PC] [Kicked]");
                            MXLog.i("PushConnectService", "[PC][Kicked]");
                            PushConnectService.this.ne();
                    }

				} else if (var1.what == 1005) {
                    boolean var5 = PushConnectService.this.ajJ.aR(PushConnectService.this);

                    if (var5) { return;}

                    if (fb.nE().isConnected()) { return; }

                    PushConnectService.this.reconnect();
                }
			}
		};

		... //注册广播
	}

	public int onStartCommand(Intent var1, int var2, int var3) {
        MXLog.log("mxpush", "[PC] [onStartCommand]");
        MXLog.log("mxpush", "[PC] [onStartCommand]pid is {}", Process.myPid());

		boolean var5 = var1.getBooleanExtra(ajM, false); //通过PushConnectService.l静态方法启动服务时，ajM=true
        this.a(false, var5, true);
	}
	

	private void a(final boolean var1, boolean var2, boolean var3) throws MqttException {
		MXLog.log("mxpush", "[PC] [initClient]");

		//先从preference_list数据表中查询push_host_config字段表示的推送url
        this.ajK = dv.H(this).jh();

        MXLog.log("mxpush", "[PC] [initClient]pushServerURI is {}", this.ajK);

		//如果数据库中没有查询到推送url，则从config.xml中查询client_conf_push_host字符串表示的推送url
        if (null == this.ajK) { 
            this.ajK = MXKit.getInstance().getKitConfiguration().getPushHost();
        }
        MXLog.log("mxpush", "[PC] [initClient]PushHost is {}", this.ajK);

		...
			PushConnectService.this.O(var1); //在onStartCommand方法中调用时，var1=true
		...
	}

	private synchronized void O(boolean var1) throws MqttException {

		this.ajL = new fc();
        this.ajL.df(this.ajK); //this.ajK是推送地址
        this.ajL.dg(this.clientId); //this.clientId在onCreate中初始化

        fb.nE().a(this.mHandler); //this.mHandler在onCreate方法中初始化

        fb.nE().a(this, this.ajL); //调用此方法会初始化 MqttClient 对象

        MXLog.log("mxpush", "[PC] [connectClient]");
		
		//UserAccount和UserToken中的字段数据都可以在"/oauth2/token"请求返回的json字符串中查找
		UserAccount var3 = (UserAccount)u.readObj("user");
        UserToken var4 = dv.H(this).jk();

		String var5 = var4.getMqtt_password();
        
        String[] var6 = new String[]{var3.getPush_channel_id()};

        int[] var7 = new int[]{0};

        this.ajJ = new ff();
        this.ajJ.setTopics(var6);
        this.ajJ.dg(this.clientId);

        fb.nE().a(this, var5, var1, var6, var7);

	}


## com.minxing.colorpicker.fb ##

	public void a(Context var1, String var2, boolean var3, String[] var4, int[] var5) throws Exception {
		MXLog.log("mxpush", "[MXMC]  [connect]");

		this.topics = var4;

		MqttConnectOptions var7 = this.f(var1, var2, var3);

		(new fa(this.mContext, 100, this.akX, var4, var5, this.all)).execute(new MqttConnectOptions[]{var7});
    }
