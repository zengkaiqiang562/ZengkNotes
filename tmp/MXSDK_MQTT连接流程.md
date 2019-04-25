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
					//在fa类的a(MqttConnectOptions var1)方法中 ，执行了MqttClient.connect(MqttConnectOptions var1)
					//方法之后调用
	
                    MXLog.log("mxpush", "[PC] >>>[Connected]");

                } else if (var1.what == 1004) { //处理 连接异常的逻辑，在fa类的a(MqttConnectOptions var1)方法中调用
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
			PushConnectService.this.O(var1); //在onStartCommand方法中调用时，var1=false
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

		//var1是false（在onStartCommand中调用执行到此处时）
		//var5是mqtt密码字符串（mqtt_password在"/oauth2/token"返回的json数据中可查）
		//var6是包含一个push_channel_id字符串的数组（push_channel_id在"/oauth2/token"返回的json数据中可查）
		//var7是包含一个数值0的数组
        fb.nE().a(this, var5, var1, var6, var7);

	}


## com.minxing.colorpicker.fb ##

	/*处理MQTT协议消息的Handler，在PushConnectService的onCreate中初始化*/
	public void a(Handler var1) {
        this.all = var1;
    }

	/*调用this.ac初始化 MqttClient 对象 
		fc对象var2中封装了 推送地址和 push_client_id */
	public void a(Context var1, fc var2) throws MqttException {
        this.mContext = var1;
        dv.H(var1).je();

        this.ac(var2.nI(), var2.getClientId());
    }

	public void ac(String var1, String var2) throws MqttException {
        this.akX = new MqttClient(var1, var2, new MemoryPersistence());

		//MqttClient中的 setPingListener方法估计是敏行自行增加的监听器，
		//在ClientComms中的checkForActivity()方法内回调监听方法
		this.akX.setPingListener(new PingListener() {
            public void onPingFinish(MqttToken var1) {
                MXLog.log("mxpush", "[MXMC][onPingFinish]pid is {}", Process.myPid());
                dv.H(fb.this.mContext).j(System.currentTimeMillis());
                dw.c(fb.this.mContext, b.yo);
            }

            public void onPingFail() {
                MXLog.log("mxpush", "[MXMC][onPingFail]pid is {}", Process.myPid());
                dw.c(fb.this.mContext, b.yp);
            }
        });

		MqttCallback var3 = new MqttCallback() {
		
			public void connectionLost(Throwable var1) {
                MXLog.log("mxpush", "[MXMC][connectionLost]");
				... //失去连接后，会重连
			}

			public void deliveryComplete(IMqttDeliveryToken var1) {
                MXLog.log("mxpush", "[MXMC] deliveryComplete ----------");
            }

			//var1表示消息主题Topic
			public void messageArrived(String var1, MqttMessage var2) throws Exception {
                MXLog.log("mxpush", "[MXMC] [messageArrived]");
				
				if (fb.this.topics != null && fb.this.topics.length > 0 && var1.equals(fb.this.topics[0])) {
					MXLog.log("mxmessage", "[push] messageArrived----");

					String var3 = var2.toString();

                    JSONObject var4 = null;
                    try {
                        var4 = JSONObject.parseObject(var3);
                    }
	
					if ("push".equals(var4.getString("type"))) {
                        MXLog.log("mxmessage", "[push]  outside msg!");

                        String var5 = var4.getString("data");

                        String var6 = null;
                        try {
                            var6 = new String(Base64.decode(var5, 2));
                        }

                        if (var6 != null && !"".equals(var6)) {
                            Intent var7 = new Intent();
                            var7.setAction("com.minxing.kit.outside.push.message");
                            var7.putExtra("outside_push_message_key", var6);

							//由 MXKitPushReceiver 接收此广播
                            fb.this.mContext.sendBroadcast(var7, MXKit.getInstance().getAppSignaturePermission());
                        }

                    } else {

                        PushDataHandleService.L(fb.this.mContext, var3);
                    }
				}
			}
		};
	}

	//var3是false（在onStartCommand中调用执行到此处时）
	//var2是mqtt密码字符串（mqtt_password在"/oauth2/token"返回的json数据中可查）
	//var4是包含一个push_channel_id字符串的数组（push_channel_id在"/oauth2/token"返回的json数据中可查）
			push_chaennel_id充当 mqtt协议中的消息主题Topic
	//var5是包含一个数值0的数组，表示消息发布的服务质量（0是至多一次）
	public void a(Context var1, String var2, boolean var3, String[] var4, int[] var5) throws Exception {
		MXLog.log("mxpush", "[MXMC]  [connect]");

		this.topics = var4;

		MqttConnectOptions var7 = this.f(var1, var2, var3);

		(new fa(this.mContext, 100, this.akX, var4, var5, this.all)).execute(new MqttConnectOptions[]{var7});
    }

	/*配置MQTT协议的连接参数MqttConnectOptions*/
	private MqttConnectOptions f(Context var1, String var2, boolean var3) throws Exception {
        MqttConnectOptions var4 = new MqttConnectOptions();
        var4.setCleanSession(false);
        var4.setUserName("mobile");
        var4.setPassword(var2.toCharArray()); //（mqtt_password在"/oauth2/token"返回的json数据中可查）
        MXLog.log("mxpush", "[PC]MQTT PWD:{}", var2);
        SSLSocketFactory var5 = this.m(var1, var3);
        var4.setSocketFactory(var5);
        return var4;
    }

## com.minxing.colorpicker.fa ##

	public class fa extends AsyncTask<MqttConnectOptions, Void, Void>

	//在fb类的 a(Context var1, String var2, boolean var3, String[] var4, int[] var5) 方法中调用时
	//var2=100, var3在fb类的ac方法中初始化，var4中包含了一个push_channel_id作为MQTT协议的消息主题，
	//var5中只有一个数值0元素，表示消息发布的服务质量（0是至多一次）
	//var6是处理MQTT消息的Handler，在PushConnectService的onCreate中初始化
	public fa(Context var1, int var2, MqttClient var3, String[] var4, int[] var5, Handler var6) {
        this.akX = var3;
        this.mContext = var1;
        this.akZ = var2; 
        this.topics = var4;
        this.akY = var5;
        this.mHandler = var6;
    }

	protected Void doInBackground(MqttConnectOptions... var1) {
        MXLog.log("mxpush", "[MCT][doInBackground]connectType is {}", this.akZ);

        switch(this.akZ) {
            case 100:
                MqttConnectOptions var2 = var1[0];
                this.a(var2);
                break;
            case 101:
                this.close(); //如果this.akX表示的MqttClient处于连接状态，则断开连接
        }
        return null;
    }

	/*此方法中处理 连接MQTT 的逻辑 */
	private void a(MqttConnectOptions var1) {
        MXLog.log("mxpush", "[MCT][conect]");

		if (this.akX.isConnected()) { //如果此 MqttClient对象已经连接过，则不再连接
            MXLog.log("mxpush", "[MCT] already connected, no need connect");

        }else {
            Message var3;
            try {
                this.akX.connect(var1); //连接 MQTT 服务器
                this.mHandler.sendEmptyMessage(1001);

                dw.c(this.mContext, b.CONNECTED);

                try {
					//订阅消息主题，this.akY表示消息发布的QoS,此处可能为0，
					//this.topic是http请求返回的json数据中push_channel_id字段的值
					//可能是 "/u/QCh6O0OCTPmS9YkDr19JrSyd-4Y="
                    this.akX.subscribe(this.topics, this.akY);

                    MXLog.log("mxpush", "[MCT]>>>[Subscribed]");

                    dw.e(this.mContext, this.akX.hashCode());

                } catch (Exception var4) {
                    MXLog.log("mxpush", "[MCT]client subscribe fail! and exception is {}", var4);
                    var4.printStackTrace();
                }
            } catch (MqttException var5) {
				...//通过在PushConnectService的onCreate中初始化的Handler处理连接异常的情况
			}
		}
	}
