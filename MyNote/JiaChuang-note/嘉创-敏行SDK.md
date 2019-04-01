## 初始化MXKit ##
初始化必须放到application中完成.

	MXKitConfiguration config = new MXKitConfiguration.Builder(LoginActivity.this)
	        //敏行server地址和敏行推送地址
	        .hostOptions("http://dev3.dehuinet.com:8013","ssl://dev3.dehuinet.com:1813")
	        .build();
	
    MXKit.getInstance().init(getApplicationContext(), config);

## 登录 ##
	MXKit.getInstance().loginMXKit(LoginActivity.this, username, password
		, new MXKitLoginListener() {
	
	            @Override
	            public void onSuccess() {
	                 //登陆成功后的逻辑
	                Intent intent = new Intent(LoginActivity.this, MXChatActivity.class);
	                startActivity(intent);
	                finish();
	            }
	
	            @Override
	            public void onFail(MXError error) {
	
	            }
	        });