## ConversationActivity.java ##

	private gc apJ;  //gc extends BaseAdapter
	
	private ConversationListView apD = null; //显示聊天信息的ListView

	private ImageButton backButton = null; //标题栏左侧back按钮
    private MXButton apo = null; //标题栏左侧的取消按钮，与back按钮重叠

    private MXButton apq = null; //标题栏右侧按钮  "确定"

	protected TextView apm = null; //标题名
    protected TextView apn = null;	//加密时的标题名，与 apm重叠

	protected ImageButton apy = null;  //标题栏右侧按钮  显示一个人头  （私聊界面）
    protected ImageButton apz = null;  //标题栏右侧按钮  显示两个人头 （群聊界面）
    private ImageButton apA = null;  //标题栏右侧按钮  显示设置图标（公众号 聊天界面）

	private ViewStub aoQ = null;  //R.id.conversation_footer_normal
    private ViewStub aoR = null;  //R.id.conversation_footer_ocu
	private ViewStub aqr;  //R.id.conversation_pop_multichoice

	private boolean apG = true; //根据此变量判断是语音输入还是文字输入, 为true则当前是文字输入

	private ImageButton apr = null;  //输入栏左侧的模式按钮（选择语音输入或文字输入）

	

	 //接受广播：
	//"com.minxing.action.message.refresh"
	//"call_ring_off"
	//"com.minxing.action.conversation.update"
	private BroadcastReceiver ajP; 
  
### mx_conversation_message_list.xml ###

	<include
        android:id="@+id/system_title"
        layout="@layout/mx_system_title" />

	<ViewStub
        android:id="@+id/conversation_footer_normal"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout="@layout/mx_conversation_message_footer"
        android:visibility="gone" />

    <ViewStub
        android:id="@+id/conversation_footer_ocu"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout="@layout/mx_conversation_message_custom_menu_footer"
        android:visibility="gone" />

    <ViewStub
        android:id="@+id/conversation_pop_multichoice"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout="@layout/mx_popup_mutichoice"
        android:visibility="gone" />

### mx_system_title.xml ###

	R.id.title_left_button
	R.id.btn_mx_system_title_cancel

	R.id.title_name
	R.id.title_secret

	R.id.title_right_save_button //标题栏右侧图标，"确定"
	R.id.right_btn_check_person_detail  //标题栏右侧图标，显示一个人头
	R.id.right_btn_check_person_multi_detail  //标题栏右侧图标，显示两个人头
	R.id.right_btn_check_person_ocu_detail  //标题栏右侧图标，显示设置图标

#### onCreate方法 ####

	public void onCreate(Bundle var1) {

		this.setContentView(layout.mx_conversation_message_list);

        this.aoO = new ConversationActivity.a();
        this.service = new com.minxing.kit.internal.core.service.c();

        this.handleIntentData(); //handleIntentData中会初始化mConversation

		this.apJ = new gc(this, this.At);
        this.apJ.setHandler(this.aoO);

		this.apJ.setMultiUser(this.mConversation.isMultiUser());
        this.apJ.setOCUConversation(this.mConversation.getOcu_id() > 0);
		if (!this.mConversation.isMultiUser()) {
            try {
                this.apJ.setDirect_to_user_id(Integer.parseInt(this.mConversation.getInterlocutor_user_ids()));
            } catch (Exception var3) {
                var3.printStackTrace();
            }
        }

		this.fi();
        this.initView();
        this.aqx = ChatController.getInstance();
        this.dlManager = com.minxing.kit.internal.core.downloader.e.aK(this);
        this.oc();
        this.apD.setAdapter(this.apJ);

		IntentFilter var2 = new IntentFilter();
        var2.addAction("com.minxing.action.message.refresh");
        var2.addAction("call_ring_off");
        var2.addAction("com.minxing.action.conversation.update");

        this.ajP = new BroadcastReceiver(){...};

		de.iy().aN(this.mConversation.getConversation_id());
        this.aqb = new ConversationActivity.d(this);
        this.aX((Context)this);
        this.of();

	}

#### initView()方法 ####

	private void initView() {

        this.apD = (ConversationListView)this.findViewById(com.minxing.kit.R.id.listview);
		
		//给listView的背景添加水印图片
        if (!this.mConversation.isSecretChat()) {
            u.a(this.apD, w.au(this).density);
        }

		this.apl = (LinearLayout)this.findViewById(com.minxing.kit.R.id.view_header);

        this.backButton = (ImageButton)this.findViewById(com.minxing.kit.R.id.title_left_button);

        this.apq = (MXButton)this.findViewById(com.minxing.kit.R.id.title_right_save_button);

        this.apo = (MXButton)this.findViewById(com.minxing.kit.R.id.btn_mx_system_title_cancel);

        this.backButton.setOnClickListener(new OnClickListener() {
            public void onClick(View var1) {
                ConversationActivity.this.oE();
            }
        });

        this.apm = (TextView)this.findViewById(com.minxing.kit.R.id.title_name);
        this.apn = (TextView)this.findViewById(com.minxing.kit.R.id.title_secret);

        this.apy = (ImageButton)this.findViewById(com.minxing.kit.R.id.right_btn_check_person_detail);

        this.apz = (ImageButton)this.findViewById(com.minxing.kit.R.id.right_btn_check_person_multi_detail);

        this.apA = (ImageButton)this.findViewById(com.minxing.kit.R.id.right_btn_check_person_ocu_detail);

        this.og();
        this.ob();
        this.oh();
        this.oi();
        this.oo();

	}

#### this.og() 方法 ####

	protected void og() {
		
		//如果是群聊，那么var1字符串数组中会有多个群聊人的id
		String[] var1 = null;
		String var2 = this.mConversation.getInterlocutor_user_ids();
        if (var2 != null && !"".equals(var2)) {
            var1 = var2.split(",");
        }

		//根据mConversation中的数据信息，判断当前聊天界面是群聊，还是私聊，并设置相应的标题
        String var3 = u.a(this, this.mConversation, var1);
        this.apm.setText(var3);

		if (this.mConversation.isMultiUser()) { //本次聊天窗口是群聊，标题栏右侧显示双人图标
            this.apz.setVisibility(0);
            if (this.aqa) {
                this.apz.setVisibility(0);
            } else {
                this.apz.setVisibility(8);
            }
        } else if (this.mConversation.isOCUConversation()) { //本次聊天窗口是公众号聊天窗口，标题栏右侧显示设置图片

			//从 数据库CONVERSATION_CIPHERED.db中的 ocu_info_list 表查询当前用户是否关注此公众号
			//关注了就进入公众号界面，否则还是进入私聊窗口
            ConversationOCUOwner var4 = dn.G(this).l(this.mConversation.getOcu_id(), this.currentUserID);
            if (var4 != null) { 
                this.apA.setVisibility(0);
            } else {
                this.apy.setVisibility(0);
            }
        } else { //本次聊天窗口是私聊，标题栏右侧显示单人图标
            this.apy.setVisibility(0);
        }

		if (this.mConversation.isOCUConversation()) {
			//进入公众号的设置界面 ContactDetailPublicActivity
            this.apA.setOnClickListener(new OnClickListener() {
                public void onClick(View var1) {
                    int var2 = ConversationActivity.this.mConversation.getOcu_id();
                    ConversationOcuInfo var3 = ConversationActivity.this.service.h(ConversationActivity.this, var2, ConversationActivity.this.currentUserID);
                    if (var3 == null || TextUtils.isEmpty(var3.getOcu_id()) || !ConversationActivity.this.aqx.launchCustomSetting(ConversationActivity.this, var3.getOcu_id(), ConversationActivity.this.mConversation.getConversation_id())) {
                        Intent var4 = new Intent(ConversationActivity.this, ContactDetailPublicActivity.class);
                        var4.putExtra("person_id", ((ContactPeople)ConversationActivity.this.apK.get(0)).getPerson_id());
                        var4.putExtra("setting_org_conversation", ConversationActivity.this.mConversation);
                        ConversationActivity.this.startActivityForResult(var4, 9992);
                    }

                }
            });
        } else {
			//进入私聊窗口的聊天信息界面ConversationSettingActivity（查看聊天记录等功能设置）
            this.apy.setOnClickListener(new OnClickListener() {
                public void onClick(View var1) {
                    Intent var2 = new Intent(ConversationActivity.this, ConversationSettingActivity.class);
                    var2.putExtra("setting_org_peoples", (Serializable)ConversationActivity.this.apK);
                    var2.putExtra("setting_vip_peoples", (Serializable)ConversationActivity.this.apL);
                    var2.putExtra("setting_org_conversation", ConversationActivity.this.mConversation);
                    ConversationActivity.this.startActivityForResult(var2, 9992);
                }
            });

			////进入群聊窗口的聊天信息界面ConversationSettingActivity（显示群成员，查看聊天记录等功能设置）
            this.apz.setOnClickListener(new OnClickListener() {
                public void onClick(View var1) {
                    Intent var2 = new Intent(ConversationActivity.this, ConversationSettingActivity.class);
                    var2.putExtra("setting_org_peoples", (Serializable)ConversationActivity.this.apK);
                    var2.putExtra("setting_vip_peoples", (Serializable)ConversationActivity.this.apL);
                    var2.putExtra("setting_org_conversation", ConversationActivity.this.mConversation);
                    ConversationActivity.this.startActivityForResult(var2, 9992);
                }
            });
        }

	}

#### this.oo() 方法 ####

	private void oo() {
        if (this.apN == null || this.apN.getOcuType() != OCU_TYPE.BASIC) {
            this.aoQ = (ViewStub)this.findViewById(com.minxing.kit.R.id.conversation_footer_normal);
            this.aoQ.inflate();
            this.on();
            if (this.apN != null && this.apN.getOcuType() == OCU_TYPE.ADVANCED && this.apN.getOcuMenus() != null && this.apN.getOcuMenus().size() > 0) {
                this.aoR = (ViewStub)this.findViewById(com.minxing.kit.R.id.conversation_footer_ocu);
                this.op();
            }
        }

        this.aqr = (ViewStub)this.findViewById(com.minxing.kit.R.id.conversation_pop_multichoice);
    }

#### this.on() 方法 ####
初始化私聊或群聊窗口底部的输入栏

	private void on() {
        this.apr = (ImageButton)this.findViewById(com.minxing.kit.R.id.message_mode_switch_btn); 
        if (this.apG) {//根据此变量判断是语音输入还是文字输入
            this.apr.setImageResource(drawable.mx_chatting_setmode_voice_btn);
        } else {
            this.apr.setImageResource(drawable.mx_chatting_setmode_keyboard_btn);
        }

		this.apr.setOnClickListener(new OnClickListener() {...}); //切换输入模式

        this.apB = (LinearLayout)this.findViewById(com.minxing.kit.R.id.text_panel); //文字输入控件的 容器UI

        this.BT = (ConversationEditText)this.findViewById(com.minxing.kit.R.id.message_content_et); //文字输入控件EditText
		//如果之前存有未发出的聊天草稿，则显示在EditText中
		if (this.mConversation.getDraftText() != null && !"".equals(this.mConversation.getDraftText())) {
            Editable var1 = this.BT.getEditableText();
            var1.append(ib.aZ(this).a(this, new SpannableStringBuilder(this.mConversation.getDraftText())));
        }

		//输入文字
		this.BT.setOnTouchListener(new OnTouchListener() {...});
		this.BT.addTextChangedListener(new TextWatcher() {...});

		//发送按钮
		this.apC = (Button)this.findViewById(com.minxing.kit.R.id.message_send_btn); 
		this.apC.setOnClickListener(new OnClickListener() {...});

		//加好按钮
		this.aps = (ImageButton)this.findViewById(com.minxing.kit.R.id.message_attach_btn);
		this.aps.setOnClickListener(new OnClickListener() {...});

		//表情按钮
		this.apv = (ImageButton)this.findViewById(com.minxing.kit.R.id.message_smile_btn);
		this.apv.setOnClickListener(new OnClickListener() {...});

		//按住说话
		this.aoJ = (Button)this.findViewById(com.minxing.kit.R.id.voice_record_bt);
		this.aoJ.setOnTouchListener(new OnTouchListener() {...});

		...	
		//初始化 插件 panel（如照片、拍摄、位置...）
		...


		//初始化表情panel
		this.gR();

	}

#### this.gR() 方法 ####
初始化表情panel，表情图标作为item放在GridView中，
采用ViewPager显示多页

	private void gR() {
        this.BW = (LinearLayout)this.findViewById(com.minxing.kit.R.id.message_smiley_panel); //表情 panel 容器
        this.BX = (ViewPager)this.findViewById(com.minxing.kit.R.id.smiley_panel_flipper); //显示表情的ViewPager
        this.BY = (LinearLayout)this.findViewById(com.minxing.kit.R.id.smiley_panel_dot);  //ViewPager指示器

		View var1 = new View(this);
        var1.setBackgroundColor(0);

		//this.Ca是List<View>集合，主要放置展现表情图标的GridView控件元素
        this.Ca.add(var1); 

		//var2 是List<List<ConversationSmiley>> 集合，封装了多个集合List<ConversationSmiley>，
		//每个List<ConversationSmiley>元素，会作为一个GridView的数据集，数据就是代表表情图标的对象ConversationSmiley
		//ib类将MXSDK的drawable-hdpi-v4文件夹下的表情图标的资源id
		//封装到ConversationSmiley对象中（除了资源id，还有表情的名字，表情的文字表示方式）
        List var2 = ib.aZ(this).aBS;  

		//cb是一个List集合，里面放置每个GridView对应的适配器 gi （gi extends BaseAdapter）
        this.Cb = new ArrayList();

		GridView var4;

        for(int var3 = 0; var3 < var2.size(); ++var3) {
            var4 = new GridView(this);

            gi var5 = new gi(this, (List)var2.get(var3));
            var4.setAdapter(var5);

            this.Cb.add(var5);
			
			//GridView的item的点击事件，如果点击的是图标，那么把对应的Bitmap封装到ImageSpan对象中的，
			//调用SpannableString.setSpan方法把ImageSpan当做字符串处理，
			//然后可以把SpannableString对象表示的字符串显示在EditText中
            var4.setOnItemClickListener(new OnItemClickListener() {...});
			...
			this.Ca.add(var4);
		}

		/* 以下初始化指示器 */

		View var7 = new View(this);
        var7.setBackgroundColor(0);
        this.Ca.add(var7);
        this.Cc = new ArrayList();

		for(int var9 = 0; var9 < this.Ca.size(); ++var9) {
            ImageView var8 = new ImageView(this);
            var8.setBackgroundResource(drawable.mx_d1);

			this.BY.addView(var8, var6); //var6是LayoutParams

			this.Cc.add(var8);
		}

		//gj extends PagerAdapter，将一个GridView作为一个page页面显示
		this.BX.setAdapter(new gj(this.Ca));

        this.BX.setCurrentItem(1);

		//切换指示器
		this.BX.setOnPageChangeListener(new OnPageChangeListener() {...});
	}


## MXSDK的 ht.java ##
发送聊天消息的入口