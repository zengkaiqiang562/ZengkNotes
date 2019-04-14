## ContactPublicGridActivity.java ##
从通讯录的“公众号”条目进入的公众号界面

	package com.minxing.kit.internal.contact;

	public class ContactPublicGridActivity extends BaseActivity

	private ContactOcuView agg = null;

	protected void onCreate(Bundle var1) {
        super.onCreate(var1);

        this.initView();

        this.agg.enableOcuChangeMonitor();
        this.agg.setCategoryId(this.categoryId);

        this.agg.loadData(true);
    }

	private void initView() {
        this.setContentView(layout.mx_public_grid);

		this.leftbutton = (ImageButton)this.findViewById(id.title_left_button); //左上角"<"返回按键，退出此Activity

		//右上角的“+”号按钮，点击跳转到ContactQueryByStarActivity界面（“添加公众号”）
		this.agh = (ImageButton)this.findViewById(id.right_btn_new_public_account); 

		this.categoryId = this.getIntent().getIntExtra(agf, -1); //agf = "ocu_category_id"，从通讯录界面的公众号条目进入时，Intent中无此参数

        this.agh.setOnClickListener(new OnClickListener() {
            public void onClick(View var1) {
				//Intent携带"ocu_category_id"参数，跳转到ContactQueryByStarActivity界面，添加公众号
                MXUIEngine.getInstance().getContactManager().addOcu(ContactPublicGridActivity.this, ContactPublicGridActivity.this.categoryId);
            }
        });

        this.agi = (Button)this.findViewById(id.finish_edit_btn); //右上角“完成”按钮，长按GridView中的某个公众号进入编辑状态后，则显示此按钮
        this.agi.setOnClickListener(new OnClickListener() {
            public void onClick(View var1) {
                ContactPublicGridActivity.this.agg.endEdit();
            }
        });

		this.agg = (ContactOcuView)this.findViewById(id.ocu_grid_view); //显示当前用户的所有公众号的容器控件

	}

	public void enableOcuChangeMonitor() {
		//监听广播"com.minxing.refresh.ocuinfos"
		//收到广播时，刷新或重新加载所有公众号
		if (var2.getAction().equals("com.minxing.refresh.ocuinfos")) {

            boolean var3 = var2.getBooleanExtra("isNeedReoload", false);
            if (var3) {
                ContactOcuView.this.loadData(false);
            } else {
                ContactOcuView.this.refresh();
            }
        }
	}

	public synchronized void loadData(boolean var1) {

        int var2 = this.currentUserInfo.getCurrentIdentity().getId();

		//根据当前用户id，从数据表 ocu_info_list 中查询当前用户的所有公众号的集合，返回List<ConversationOCUOwner>
        List var3 = this.service.s(this.mContext, var2);


		//如果var1为true，表示需要从网络获取当前用户的所有公众号，
		//调用requestSubscribedPublicAccount(List<ConversationOCUOwner> var1)方法从网络获取，参数集合不为null时，表示需要更新的公众号集合
		//url地址为"/api/v1/subscriptions/ocus"，返回的json数据会解析成List<ConversationOCUOwner>集合
	
		//刷新GridView的适配器数据

		
		
	}


## mx_public_grid.xml ##

	<include
        android:id="@+id/system_title"
        layout="@layout/mx_system_title" />

	<com.minxing.kit.ui.contacts.ContactOcuView
        android:id="@+id/ocu_grid_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_below="@id/system_title" />


## ContactOcuView ##
公众号界面，显示所有公众号的容器控件

	package com.minxing.kit.ui.contacts;

	private List<ConversationOCUOwner> ocuList = new ArrayList(); //公众号集合

	public class ContactOcuView extends LinearLayout

	private void init() {

		this.content = LayoutInflater.from(this.mContext).inflate(layout.mx_ocu_grid_view, (ViewGroup)null);

        this.gridView = (GridView)this.content.findViewById(id.mx_gridview);

		this.service = new c();

        this.adapter = new ef(this.mContext, this.ocuList);

        this.adapter.setHandler(new ContactOcuView.PublicGridHandler());

		this.gridView.setAdapter(this.adapter);

		this.gridView.setOnItemClickListener(new OnItemClickListener() {
            public void onItemClick(AdapterView<?> var1, View var2, int var3, long var4) {
				ConversationOCUOwner var6 = (ConversationOCUOwner)ContactOcuView.this.ocuList.get(var3);

				if (!var6.isPlaceHolder()) {
					//从数据表 conversation_list 中查询本地是否保存有跟此公众号的聊天记录信息
                    Conversation var7 = dn.G(ContactOcuView.this.mContext).a(var6.getPublic_person_id(), ContactOcuView.this.currentUserInfo.getCurrentIdentity().getId(), (String)null, false);
					//点击某个公众号会跳转到此公众号的聊天窗口
                    final Intent var8 = new Intent(ContactOcuView.this.mContext, ConversationActivity.class);
                    if (var7 != null) {
						//本地保存有跟此公众号的聊天记录信息
                        var8.putExtra("conversation_object", var7);
                        u.a(ContactOcuView.this.mContext, var7, var8);
                        ContactOcuView.this.mContext.startActivity(var8);
                    } else {
						//本地没有保存跟此公众号的聊天记录信息，从服务器地址u“/api/v1/conversations/about_ocu/%d” 中查询跟此公众号的聊天记录信息
                        (new c()).d(var6.getPublic_person_id(), new n(ContactOcuView.this.mContext, true, ContactOcuView.this.mContext.getResources().getString(string.mx_warning_dialog_title), ContactOcuView.this.mContext.getResources().getString(string.mx_warning_dialog_in_progress)) {
                            public void success(Object var1) {
                                Conversation var2 = (Conversation)var1;
                                if (var2 != null) {
                                    var2.setDraft("true");
                                    var8.putExtra("conversation_object", var2);
                                    this.mContext.startActivity(var8);
                                }

                            }

                            public void failure(MXError var1) {
                                super.failure(var1);
                            }
                        });
                    }

                }
			}
		})；
	}

## ContactQueryByStarActivity ##
添加公众号的界面

	package com.minxing.kit.internal.contact;

	private List<ConversationOCUOwner> ocuList = new ArrayList(); //当前用户未添加的公众号集合

	protected void onCreate(Bundle var1) {
        super.onCreate(var1);

        this.initView();

        this.categoryId = this.getIntent().getIntExtra(ContactPublicGridActivity.agf, -1);

        this.service = new c();

        this.listView.setOnItemClickListener(new OnItemClickListener() {
            public void onItemClick(AdapterView<?> var1, View var2, int var3, long var4) {
				//点击未添加的公众号，进入该公众号的详细信息界面ContactDetailPublicActivity
                ContactQueryByStarActivity.this.b((ConversationOCUOwner)ContactQueryByStarActivity.this.ocuList.get(var3));
            }
        });

		//调用此方法，会从服务器地址"/api/v1/ocus/unsubscribed?ocu_type=1"中获取未订阅的公众号信息，
		//返回的json数据被封装成List<ConversationOCUOwner>集合
        this.mc();
    }

	private void initView() {
		this.setContentView(layout.mx_contact_search_star);

		this.listView = (ListView)this.findViewById(id.contact_list);
	}

	private void prepareViewData() {

        this.agx = new em(this, this.ocuList); //this.agx是ListView的适配器

        this.listView.setAdapter(this.agx);
        this.agx.notifyDataSetChanged();
    }