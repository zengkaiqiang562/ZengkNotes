###  ContactDetailActivity  ###
此界面显示联系人详细信息

	private WBPersonExtension aeP = null; //执行this.lV()方法后，通过http请求返回的联系人详细信息 的 对象

	private boolean afc = false; //是否可添加此联系人为好友

**onCreate方法**

	this.setContentView(layout.mx_contact_switch_detail);

    this.Fz = (LinearLayout)this.findViewById(id.contact_detail_content);

    this.personID = this.getIntent().getIntExtra("person_id", -999);

    UserAccount var2 = df.iA().iB();

    if (var2 != null && var2.getCurrentIdentity() != null) {
        this.currentUserID = var2.getCurrentIdentity().getId();
    }

	//是否可添加此联系人为好友
	if (MXUIEngine.getInstance().getContactManager().isAddNewFriendEnabled(this)) {
        if (!dn.G(this).x(this.currentUserID, this.personID) && this.currentUserID != this.personID) {
            this.afc = true;
        } else {
            this.afc = false;
        }
    } else {
        this.afc = false;
    }

	this.zP = new b();
    this.initView();
    this.lV();

**mx_contact_switch_detail.xml**
	
	<include
        android:id="@+id/system_title"
        layout="@layout/mx_system_title" />

**mx_system_title.xml**

	id.title_name
	id.title_left_button

**initView方法**
	this.titleName = (TextView)this.findViewById(id.title_name); //HeadView 的 标题
	this.titleLeftButton = (ImageButton)this.findViewById(id.title_left_button); //HeadView 左侧的返回图标
	this.titleLeftButton.setVisibility(0);
	this.titleLeftButton.setOnClickListener(new OnClickListener() {
	    public void onClick(View var1) {
	        ContactDetailActivity.this.finish();
	    }
	});
	this.OP = (TextView)this.findViewById(id.person_name); //联系人名字
	this.aeT = (TextView)this.findViewById(id.confirm_add_delete); //"加为联系人|删除联系人"
	this.avatar = (ImageView)this.findViewById(id.avatar); //显示头像的ImageView
	this.aeU = (LinearLayout)this.findViewById(id.mx_person_circle); //工作圈
	this.aeV = (LinearLayout)this.findViewById(id.mx_person_files); //文件
	this.aeW = (Button)this.findViewById(id.send_sms);  //底部"发消息" 按钮
	this.firstloading = (ProgressBar)this.findViewById(id.firstloading);
	this.aeX = (RelativeLayout)this.findViewById(id.person_detail_contianer); //顶部HeadView和底部"发消息"之间的布局空间
	this.aeY = (Switch)this.findViewById(id.mx_swith_attention);  //特别关注开关
	this.aeZ = (LinearLayout)this.findViewById(id.send_layout);
	this.afa = (LinearLayout)this.findViewById(id.add_or_delete_layout);
	this.afb = (RelativeLayout)this.findViewById(id.mx_spacial_attention);

	//判断布局容器this.aeX的背景是否需要添加水印
	//需要添加的话，会发出http请求 http://192.168.0.69/photos/watermark?loginname=" + 登录名字 + "&" + 用户名
	//通过universal-imageloader框架发请求，返回一张作为背景的水印图片
	u.a(this.aeX, w.au(this).density); 

**this.lV() 方法**

	private void lV() {
        this.firstloading.setVisibility(0);
        this.aeX.setVisibility(8);
        this.aeZ.setVisibility(8);
        n var1 = new n(this) {
            public void success(Object var1) { //返回表示 http请求返回的联系人的详细信息的 WBPersonExtension对象
                super.success(var1);

                ContactDetailActivity.this.aeP = (WBPersonExtension)var1;

                if (ContactDetailActivity.this.currentUserID != -999) {

                    ContactDetailActivity.this.aeQ = ContactDetailActivity.this.zP.d(ContactDetailActivity.this, 
							ContactDetailActivity.this.currentUserID, ContactDetailActivity.this.personID);

                    ContactDetailActivity.this.aeR = ContactDetailActivity.this.zP.f(ContactDetailActivity.this, 
							ContactDetailActivity.this.currentUserID, ContactDetailActivity.this.personID);
                }

                ContactDetailActivity.this.firstloading.setVisibility(8);
                ContactDetailActivity.this.aeX.setVisibility(0);
                ContactDetailActivity.this.aeZ.setVisibility(0);

                ContactDetailActivity.this.initScreen();
            }

            public void failure(MXError var1) {
                ContactDetailActivity.this.firstloading.setVisibility(8);
                super.failure(var1);
                ContactDetailActivity.this.finish();
            }
        };

        this.zP.b(this.personID, this.currentUserID, var1);
    }

**initScreen()方法**

	if (this.personID == this.currentUserID) {
        this.titleName.setText(string.mx_more_current_account_info); //个人信息
    } else {
        this.titleName.setText(this.aeP.getName());
    }

	//根据WBPersonExtension封装的联系人信息中的头像图片url地址，从网络获取头像图片（ImageLoader）
	this.c(this.aeP.getAvatar_url(), this.avatar); 

	if (this.afc) { //如果当前联系人还不是好友，则底部按钮由“发消息”改为“加为好友”，跳转到添加好友的界面
        this.aeW.setText(string.mx_search_add_new_friend_btn_text);
        this.aeW.setOnClickListener(new OnClickListener() {
            public void onClick(View var1) {
                Intent var2 = new Intent(ContactDetailActivity.this, ContactVerificationActivity.class);
                var2.putExtra("personal_id", ContactDetailActivity.this.aeP.getId());
                ContactDetailActivity.this.startActivity(var2);
            }
        });
    }

	if (c.aG(this).mZ()) {

        this.aeW.setOnClickListener(new OnClickListener() {
            public void onClick(View var1) {
                Conversation var2 = dn.G(ContactDetailActivity.this).a(ContactDetailActivity.this.aeP.getId(), ContactDetailActivity.this.currentUserID, (String)null, false);
                Intent var3 = new Intent(ContactDetailActivity.this, ConversationActivity.class);
                if (var2 != null) {
                    var3.putExtra("conversation_object", var2);
			
					//处理未读消息	
					//1. 将Conversation对象var2中的未读消息的数量通过"conversation_unread"封装到Intent对象var3中
					//2. 关闭 显示未读消息的通知。
					
                    u.a(ContactDetailActivity.this, var2, var3);
                } else {
                    di.C(ContactDetailActivity.this).a(ContactDetailActivity.this.aeP);
                    var2 = new Conversation();
                    var2.setMulti_user("false");
                    var2.setInterlocutor_user_ids(String.valueOf(ContactDetailActivity.this.aeP.getId()));
                    var2.setCreator_id(ContactDetailActivity.this.currentUserID);
                    var2.setUpdate_at(String.valueOf(System.currentTimeMillis()));
                    var2.setDraft("true");
                    var2.setTop_at("");
                    var2.setCurrent_user_id(ContactDetailActivity.this.currentUserID);
                    var2.setConversation_id(var2.hashCode());
                    var3.putExtra("conversation_object", var2);
                }

                var3.putExtra("conversation_back", true);
                var3.setFlags(67108864);
                ContactDetailActivity.this.startActivity(var3);
            }
        });

        if (MXKit.getInstance().getKitConfiguration().isContactVip()) {
            this.aeY.setOnCheckedChangeListener(new OnCheckedChangeListener() {
                public void onCheckedChanged(CompoundButton var1, boolean var2) {
                    ContactDetailActivity.this.zP.b(!ContactDetailActivity.this.aeR, ContactDetailActivity.this.aeP.getId(), new n(ContactDetailActivity.this, true, ContactDetailActivity.this.getResources().getString(string.mx_warning_dialog_title), ContactDetailActivity.this.getResources().getString(string.mx_warning_dialog_in_progress)) {
                        public void success(Object var1) {
                            if (ContactDetailActivity.this.aeR) {
                                ContactDetailActivity.this.zP.e(ContactDetailActivity.this, ContactDetailActivity.this.currentUserID, ContactDetailActivity.this.aeP.getId());
                            } else {
                                ArrayList var2 = new ArrayList();
                                var2.add(ContactDetailActivity.this.d(ContactDetailActivity.this.aeP));
                                ContactDetailActivity.this.zP.d(ContactDetailActivity.this, ContactDetailActivity.this.currentUserID, var2);
                            }

                            ContactDetailActivity.this.aeR = !ContactDetailActivity.this.aeR;
                        }

                        public void failure(MXError var1) {
                            super.failure(var1);
                        }
                    });
                }
            });
        }
	}

----
this.zP = new b();

ContactDetailActivity.this.aeP = (WBPersonExtension)var1;

this.personID = this.getIntent().getIntExtra("person_id", -999);

UserAccount var2 = df.iA().iB();
if (var2 != null && var2.getCurrentIdentity() != null) {
    this.currentUserID = var2.getCurrentIdentity().getId();
}

---------
### 添加联系人：AddPersonalContactActivity.java ###

	package com.minxing.kit.internal.contact;

onCreate()
	this.initView();
    this.zP = new b();
	this.listView.setOnItemClickListener(new OnItemClickListener() {

		//onItemClick方法参数声明：(AdapterView<?> parent, View view, int position, long id)
        public void onItemClick(AdapterView<?> var1, View var2, int var3, long var4) {

            ContactQueryByNameActivity.this.d((ContactPeople)ContactQueryByNameActivity.this.ags.get(var3));
        }
    });


this.d(ContactPeople var1)
	//参数var1是通过http请求返回的响应json数据得到的
	private void d(ContactPeople var1) {
        Intent var2 = new Intent(this, ContactDetailActivity.class);
        var2.putExtra("person_id", var1.getPerson_id());
        var2.putExtra("searched_person_detail", true);
        var2.addFlags(67108864);
        this.startActivity(var2);
    }
	

initView()
	 RelativeLayout var1 = (RelativeLayout)LayoutInflater.from(this).inflate(layout.mx_contact_search_main, (ViewGroup)null);
    this.setContentView(var1);

	TextView var2 = (TextView)this.findViewById(id.title_name);
	var2.setText(string.mx_top_right_add_contact);

	this.aew = (RelativeLayout)this.findViewById(id.search_by_name);  按姓名查找
	this.aex = (RelativeLayout)this.findViewById(id.find_more_star_friend); 添加公众号
	this.aey = (RelativeLayout)this.findViewById(id.search_contacts);公司通讯录
	this.aez = (RelativeLayout)this.findViewById(id.search_add_new_friend);添加新好友
	this.aeE = this.findViewById(id.view_mx_contact_search_main_friend_divider); (只是一条分割线)

mx_contact_search_main.xml
	<include
        android:id="@+id/system_title"
        layout="@layout/mx_system_title" />

mx_system_title.xml
	id.title_name

按姓名查找点击事件：
	this.aew.setOnClickListener(new OnClickListener() {
        public void onClick(View var1) {
            Intent var2 = new Intent(AddPersonalContactActivity.this, ContactQueryByNameActivity.class);
            AddPersonalContactActivity.this.startActivity(var2);
        }
    });

-------------
**搜姓名 ContactQueryByNameActivity.java**

	private List<IContact> ags = new ArrayList(); //ListView中item展示的内容集合
	private ei agu = null;  //继承自BaseAdapter的ListView的适配器

initView()
	this.setContentView(layout.mx_contact_search_name);
	this.listView = (ListView)this.findViewById(id.contact_list);
    this.zF = (EditText)this.findViewById(id.searchName); 请输入姓名或拼音
	this.zH = (ImageView)this.findViewById(id.remove_icon); 清除EditText的图标
	this.leftbutton = (ImageButton)this.findViewById(id.title_left_button); 退出此Activity的返回图标
	this.agr = (Button)this.findViewById(id.title_right_button); 查找

 	this.system_titleName = (TextView)this.findViewById(id.title_name);
    this.system_titleName.setText(string.mx_search_name); 搜姓名

mx_contact_search_name.xml
	<include
        android:id="@+id/system_title"
        layout="@layout/mx_system_title" />  <!--HeadView 的实现-->

mx_system_title.xml
	id.title_name
	id.title_left_button

查找的点击事件
	this.agr.setOnClickListener(new OnClickListener() {
        public void onClick(View var1) {
            ContactQueryByNameActivity.this.mb();
        }
    });

this.mb()方法实现：
	private void mb() {
        String var1 = this.zF.getText().toString(); //var1表示EditText中输入的姓名关键字
        if (var1 != null && var1.trim().length() != 0) {
			//r 是MXSDK中的工具类，cd 方法对var1字符串做过滤，不能是数字开头的纯数字，
			//当var1符合正则表达式"^\\d+$"返回true
            if (r.cb(var1)) { 
				//u 是MXSDK中的工具类， b 方法用于弹出Toast提示信息 "请输入要查询的姓名"
                u.b(this, this.getString(string.mx_toast_please_input_name_to_search), 0);
            } else {
                this.cN(var1); //此方法真正实现查询功能
            }
        } else {
            this.ags.clear();//清除ListView
            this.prepareViewData();
            this.nodata.setVisibility(8); //8表示GONE
        }

    }

prepareViewData() 方法实现
	用于初始化ListView，并设置它的适配器 agu 
	private void prepareViewData() {
        this.agu = new ei(this.ags, this, 100, 200, false, 0, (List)null);
        this.listView.setAdapter(this.agu);
        this.agu.notifyDataSetChanged();
    }

this.cN(String var1) 方法的实现
	//此方法真正实现查询功能
	private void cN(String var1) {  //var1表示要查询的姓名关键字
        this.ags.clear();
        this.agr.setEnabled(false);
        this.zP.e(var1, new n(this) { //this.zP是 MXSDK中 b 类 的一个对象
            public void success(Object var1) {

				//var1 是一个List<IContact>集合，是本次查询http请求返回的响应数据（Json格式） 封装后的结果
				//IContact可能是ContactDepartment，或ContactPeople

                ContactQueryByNameActivity.this.firstloading.setVisibility(8);
                List var2 = (List)var1;
                ContactQueryByNameActivity.this.ags.addAll(var2);
                if (ContactQueryByNameActivity.this.ags.isEmpty()) {
                    ContactQueryByNameActivity.this.nodata.setVisibility(0);
                } else {
                    ContactQueryByNameActivity.this.nodata.setVisibility(8);
                }

                ContactQueryByNameActivity.this.prepareViewData();
                ContactQueryByNameActivity.this.agr.setEnabled(true);
            }

            public void failure(MXError var1) {
                super.failure(var1);
                ContactQueryByNameActivity.this.agr.setEnabled(true);
            }
        });
    }

### MXSDK中的 b.java ###
b 类是一个服务类（不是继承自Service的服务类），处理 HTTP请求的入口
	package com.minxing.kit.internal.core.service;
	public class b

	private eu alU = new eu() {
		//参数是封装了原始的响应实体数据（根据 d类的 isAjax标记，可能包含响应头信息）的Json格式字符串的JsonObject对象
        public void success(Object var1) {
            dr var2 = new dr();
			//var2.a((JSONObject)var1, -999)返回的是一个List<IContact>集合，
			//IContact可能是ContactDepartment，或ContactPeople
            this.mCallBack.success(var2.a((JSONObject)var1, -999));
        }

        public void failure(MXError var1) {
            this.mCallBack.failure(var1);
        }
    };

	public void e(String var1, n var2) {
        d var3 = new d();
        var3.a(MXMethod.GET);
        ArrayList var4 = new ArrayList();
        if (var1 != null && !"".equals(var1)) {
            var4.add(new BasicNameValuePair("q", var1));
        }

        var4.add(new BasicNameValuePair("limit", "50"));
        var3.setHeaders((TreeMap)null);
        var3.H((List)null);
        var3.setParams(var4);
        var3.a(MXInterface.CONTACT_SEARCH);
        this.alU.setViewCallBack(var2);
        (new ew(this.alU)).b(var3);
    }

**b(int var1, int var2, n var3) 方法**
此方法发送http请求，http://192.168.0.69/api/v1/users/%s?include_user_info=true&is_followed_by=%s
其中第一个占位符 %s 由方法参数 var1 提供， 表示联系人的id
第二个占位符 %s 由方法参数 var2 提供，表示当前用户的id
方法参数var3是监听回调对象

http请求的响应数据是 联系人详细信息 界面中需要展示的数据
	
	public void b(int var1, int var2, n var3) {
        ArrayList var4 = new ArrayList();
        d var5 = new d();
        var5.a(MXMethod.GET);
        var5.a(MXInterface.GET_PERSONAL_INFO.insertParam(new Object[]{String.valueOf(var1), String.valueOf(var2)}));
        var5.setHeaders((TreeMap)null);
        var5.H((List)null);
        var5.setParams(var4);
        eu var6 = new eu() {
            public void success(Object var1) {
                WBPersonExtension var2 = (WBPersonExtension)(new WBPersonExtension()).mapToBean(var1);
                Object var3 = ((HashMap)var1).get("user_info");
                if (var3 != null) {
                    WBPersonExtension var4 = (WBPersonExtension)(new WBPersonExtension()).mapToBean(var3);
                    var2.merge(var4);
                }

                Object var6 = ((HashMap)var1).get("user_show");
                if (var6 != null) {
                    List var5 = (new UserDetailShow()).collectionConvert(var6, UserDetailShow.class);
                    var2.setUserShow(var5);
                }

				//更新 CONVERSATION_CIPHERED.db 中 personal_contact_list 表中的指定联系人的信息
                dn.G(this.mContext).b(var2); 

				//更新 CONVERSATION_CIPHERED.db 中 vip_contact_list 表中 指定联系人的信息，
				//vip_contact_list表应该是特别关注的联系人表
                dn.G(this.mContext).c(var2);

				// 将WBPersonExtension 中部分 跟联系人相关的数据 插入到 LOCALCACHE_CIPHERED.db中的 cache_person_list 表中
                di.C(this.mContext).a(var2);

                this.mCallBack.success(var2);
            }

            public void failure(MXError var1) {
                this.mCallBack.failure(var1);
            }
        };
        var6.setViewCallBack(var3);
        (new ew(var6)).b(var5);
    }

### MXSDK中的 dr.java ###
此类用于将JsonObject转成一个JavaBean

	public class dr
		
	//var1表示的json字符串中要包含key为"items"，且value为json数组的字符串
	public List<IContact> a(JSONObject var1, int var2) {
        if (var1 == null) {
            return null;
        } else {
            JSONArray var3 = (JSONArray)var1.get("items");
            return this.a(var3, var2);
        }
    }

	//var1参数是key为"items"的value值，此value值是一个json数组字符串
	public List<IContact> a(JSONArray var1, int var2) {
        ArrayList var3 = new ArrayList();
        if (var1 == null) {
            return null;
        } else {
            Iterator var4 = var1.iterator();

            while(var4.hasNext()) {
                Object var5 = var4.next();
                JSONObject var6 = (JSONObject)var5;

				//遍历items表示的json数组中的每个元素，且元素也表示一个JSONObject

                if ("department".equals(var6.getString("type"))) {

                    ContactDepartment var7 = new ContactDepartment(ContactType.DEPARTMENT);
                    ...//将JSONObject中的数据封装到ContactDepartment中
                    var3.add(var7);

                } else {
				
					//此处，json数组元素的字符串形式为：
					   {
			  	        "id":205,
			  	        "name":"安理",
			  	        "type":"user",
			  	        "pinyin":"anli",
			  	        "avatar_url":"/photos/user_photo_2",
			  	        "email":"",
			  	        "cellvoice1":"",
			  	        "display_order":9999,
			  	        "login_name":"lal",
			  	        "cellvoice2":null,
			  	        "workvoice":null,
			  	        "extension":null,
			  	        "dept_name":"教学单位/学前教育单位",
			  	        "dept_id":6,
						"dept_code":"001002001",
			  	        "call_phones":"",
			  	        "permission":7,
			  	        "title":"admin",
			  	        "online":"mobile"
			  	      }

                    ContactPeople var10 = new ContactPeople(ContactType.PEOPLE);

                    ...//将JSONObject中的数据封装到ContactPeople中

                    try {
                        var10.setDept_id(var6.getInteger("dept_id"));
                    } catch (Exception var9) {
                        var9.printStackTrace();
                    }

                    if (var6.containsKey("online")) {
                        var10.setOnline(var6.getString("online"));
                    }

                    if (var2 != -999) {
                        var10.setDept_id(var2);
                    }

                    var3.add(var10);
                }
            }

            return var3;
        }
    }

### MXSDK中的 a.java ###
	package com.minxing.kit.internal.core;
	public abstract class a

	public a(Context var1, boolean var2, String var3, String var4) { 
		//参数var2，var3，var4跟弹出提示对话框相关，跟事件回调无关，无需关注
        this.mContext = var1;
        this.dialogContent = var4;
        this.dialogTitle = var3;
        this.isShowProgressDialog = var2;
    }

	public abstract void success(Object var1); //请求成功的回调， 

    public abstract void failure(MXError var1); //请求失败的回调

### MXSDK中的 n.java ###
n 类只是一个监听回调类，通过重写它的success(Object var1) 方法 处理请求成功的返回结果
重写failure(MXError var1) 方法处理请求失败的返回结果
	package com.minxing.kit.internal.core.service
	public class n extends a

### MXSDK中的 eu.java ###
eu 类是一个抽象类，继承自 a类，所以 eu类 也是一个 监听回调类，
此类的目的是包装，它可以把回调的结果做一次处理后，得到处理后的结果，再传递给 mCallBack监听回调类中的回调方法。
参考MXSDK b 类中的 alU 成员变量
	
	package com.minxing.colorpicker;
	public abstract class eu extends a

	public a mCallBack;

	public void setViewCallBack(a var1) {
        if (var1 != null) {
            this.mCallBack = var1;
            this.mContext = var1.getContext();
            this.isShowProgressDialog = var1.isShowProgressDialog();
            this.dialogTitle = var1.getDialogTitle();
            this.dialogContent = var1.getDialogContent();
        }
    }


### MXSDK中的 d.java ###
d 类用于封装请求参数
	package com.minxing.kit.internal.core
	public class d

	private MXMethod ajV; //ajV表示请求方式
    private MXInterface ajW; //ajW表示请求类型（本次请求的目的是什么，如查找联系人）
	private TreeMap<String, String> headers;
    private List<NameValuePair> params;
    private List<UploadFile> ajX; //ajX表示要上传的文件集合

	private boolean isAjax = false; //是否需要把响应头信息 封装到Json字符串中，作为响应数据一起返回

	//aka字符串用来表示请求地址url的部分内容，可能是path部分，
	//如果aka为null，那么请求地址则使用MXInterface中的formatFace字符串
	private String aka; 

	public MXMethod nf() { //nf 方法用于返回 d对象中封装的请求方式
        return this.ajV;
    }

    public void a(MXMethod var1) { //a(MXMethod var1) 方法用于 设置 d对象中的请求方式
        this.ajV = var1;
    }

	public MXInterface ng() {//nf 方法用于返回 d对象中封装的请求类型
        return this.ajW;
    }

    public void a(MXInterface var1) {//a(MXInterface var1) 方法用于 设置 d对象中的请求类型
        this.ajW = var1;
    }

	public List<UploadFile> nh() { //nh 方法返回 d对象中封装的要上传的文件集合
        return this.ajX;
    }

    public void H(List<UploadFile> var1) {  //H 方法设置 d对象中封装的要上传的文件集合
        this.ajX = var1;
    }

	public TreeMap<String, String> getHeaders() {
        return this.headers;
    }

    public void setHeaders(TreeMap<String, String> var1) { 设置 d对象中的 请求头信息
        this.headers = var1;
    }

	public List<NameValuePair> getParams() {
        return this.params;
    }

    public void setParams(List<NameValuePair> var1) { 设置 d对象的 请求参数集合 params，可用于url中的查询条件
        this.params = var1;
    }

	public void cT(String var1) {
        this.aka = var1;
    }

    public String nk() {
        return this.aka;
    }

### MXSDK中的 MXMethod.java ###
MXMethod是一个枚举类，封装请求方式，如GET、POST等
	package com.minxing.kit.internal.core
	public enum MXMethod

	GET("GET"),
    POST("POST"),
    PUT("PUT"),
    DELETE("DELETE");

	private String mMethod;

    private MXMethod(String var3) {
        this.mMethod = var3;
    }

    public String getMethod() {
        return this.mMethod;
    }

### MXSDK中的 ew.java ###
ew 类继承自AsyncTask，表示ew类 是用于在后台处理线程任务，并返回结果
	package com.minxing.colorpicker; 
	//d类型表示执行后台任务时传入的参数Params, Object类型表示返回的结果类型
	public class ew extends AsyncTask<d, Void, Object> 

	private static ExecutorService akA = Executors.newScheduledThreadPool(5); //ew类中自定义了一个线程池 akA

	public ew(a var1) { //构造方法中传入的var1一个监听回调对象
        this.mCallBack = var1;
        this.mContext = var1.getContext();
    }

	public void b(d var1) {
        if (VERSION.SDK_INT >= 11) { 
			//Android3.0 之后使用自定义的线程池 akA 执行线程任务，避免 AsyncTask默认的串行执行
            this.executeOnExecutor(akA, new d[]{var1});
        } else {
            super.execute(new d[]{var1});
        }
    }

	// 此方法就是 AysncTask的 doInBackground方法
	public Object a(d... var1) { 
        d var2 = var1[0];
        if (var2 == null) {
            return var2;
        } else {
            String var3 = null;
            if (var2.nk() == null) {
                MXInterface var4 = var2.ng();
                var3 = var4.getFormatFace();
            } else {
                var3 = var2.nk();
            }

            return var2.nj() ? this.c(var2) : this.d(var2);
        }
    }

	private Object d(d var1) {
        return this.a(new ev(), var1);
    }

	private Object a(ev var1, d var2) {
        Object var3 = null;

        ...
                var3 = var1.a(this.mContext, var2);
        ...

        return var3;
    }

	//参数是封装了原始的响应实体数据（根据 d类的 isAjax标记，可能包含响应头信息）的Json格式字符串的JsonObject对象
	public void onPostExecute(Object var1) {
        super.onPostExecute(var1);
        if (this.mCallBack != null) {
            if (this.mCallBack.isShowProgressDialog()) {
                try {
                    if (this.QD.isShowing()) {
                        this.QD.dismiss();
                        this.h(var1);
                        return;
                    }
                } catch (Exception var3) {
                    Log.e("Exception", "" + var3);
                }
            }

            this.h(var1);
        }
    }
	
	//参数是封装了原始的响应实体数据（根据 d类的 isAjax标记，可能包含响应头信息）的Json格式字符串的JsonObject对象
	private void h(Object var1) {
        if (var1 == null) {
            MXError var2 = new MXError();
            var2.setErrorCode(-1000);
            String.format(this.mContext.getString(string.mx_error_server), String.valueOf(var2.getErrorCode()));
            this.mCallBack.failure(var2);
        } else if (this.akP) {
            this.mCallBack.success(var1);
        } else {
            this.mCallBack.failure((MXError)var1);
        }

    }

### MXSDK中的 ev.java ###
ev 类中 通过HttpClient框架发送 HTTP请求
	package com.minxing.colorpicker;
	public class ev

	public Object a(Context var1, d var2) throws Exception {
		...
		String var3 = this.a(var2);
        Class var4 = var2.ng().getClazz();
		...

		else {
            try {
				//通过fastJsong框架解析Json字符串 var3，其中var4来自MXInterface，通常就是一个Object
				//返回JsonObject对象
                Object var6 = JSON.parseObject(var3, var4);  
                return var6;
            } catch (Exception var15) {
                ...
            }
        }
	}

	//此方法正在处理HTTP请求，此方法返回的是响应实体数据（根据 d类的 isAjax标记，可能包含响应头信息）的Json格式字符串
	protected String a(d var1) throws Exception {
		MXMethod var2 = var1.nf();
        TreeMap var3 = var1.getHeaders();
        List var4 = var1.getParams();
        List var5 = var1.nh();

		String var6 = null; // var6 表示 url地址
        if (var1.nk() == null) {
            MXInterface var7 = var1.ng();
            var6 = var7.getFormatFace(); //按姓名查找联系人时，走这里，var为 "/api/v1/departments/search"
        } else {
            var6 = var1.nk();
        }

		//通过MXKit的getKitConfiguration方法得到的 serverHost是在 MXKit的init方法中传入的MXKitConfiguration中设置的。
		//MXKit的init方法在AppApplication.java中的onCreate方法中调用。
		//serverHost从config.xml中的"client_conf_http_host"字符串获取，即 http://192.168.0.69

        if (!var6.startsWith("http") && !var6.startsWith(MXKit.getInstance().getKitConfiguration().getServerHost())) {
            var6 = MXKit.getInstance().getKitConfiguration().getServerHost() + var6;
        }

		//对于按姓名查找联系人，执行到此处时，var6 为 http://192.168.0.69/api/v1/departments/search

		Object var28 = null; //var28 代表请求方式。如HttpGet,HttpPost
        String var8 = null; //将HTTP请求的响应实体内容，转换成字符串返回，
        HttpClient var9 = this.de(var6);
        Iterator var11; //用于遍历 d类中 的 params集合，
        String var13;
        String var14;

		if (var2 == MXMethod.GET) {
            if (var4 != null) {
                StringBuilder var10;
                if (var6.indexOf("?") != -1) {
                    var10 = new StringBuilder(var6 + "&");
                } else {
                    var10 = new StringBuilder(var6 + "?");
                }

                var11 = var4.iterator();

                while(var11.hasNext()) {
                    NameValuePair var12 = (NameValuePair)var11.next();
                    var13 = var12.getName();
                    var14 = URLEncoder.encode(var12.getValue(), "UTF-8");
                    var10.append(var13 + "=" + var14 + "&");
                }

                var6 = var10.deleteCharAt(var10.length() - 1).toString();
            }

			//以上通过 d类中的 params集合元素，构建 url中的查询参数，返回完整的url地址 var6

            var28 = new HttpGet(var6);
        }

		如果是post请求，url地址不需要带查询参数
		if (var2 == MXMethod.POST) {
            var28 = new HttpPost(var6);
        }

		...//如果 d类中包含请求头字段，则在var28 表示的请求方式对象中 构建请求头信息
		
		...//如果 请求方式为POST，PUT，并需要上传表单，则 在var28 表示的请求方式对象中 构建表单（如上传文件）

	
		...//构建其他请求头信息


		UserToken var34 = null;

        try {
            var34 = dv.H(this.mContext).jk(); //从数据库中获取授权令牌信息
        } catch (Exception var27) {
            var27.printStackTrace();
        }

		//如果url地址是服务器地址（http://192.168.0.69），并且不是一个oauth2.0的授权地址（path为/oauth2/token），
		//并且能够从数据库中得到用户令牌Token，那么本次请求的请求头中需要添加"Authorization"字段，授权令牌从数据库中获取
        String var38 = MXKit.getInstance().getKitConfiguration().getServerHost();
        if (var6.startsWith(var38) && !var6.endsWith(MXInterface.OAUTH2.getFormatFace()) 
				&& var34 != null && !TextUtils.isEmpty(var34.getAccess_token())) {

            if (var48) {
                Log.e("MXHttpClient", "add header key Authorization " + var34.getAccess_token());
            }

            if (((HttpUriRequest)var28).getHeaders("Authorization") == null || 
						((HttpUriRequest)var28).getHeaders("Authorization").length == 0) {

                ((HttpUriRequest)var28).addHeader("Authorization", "Bearer " + var34.getAccess_token());
            }
        }

		//从数据库中查询 当前用户在请求url地址var6时，需要附件的额外请求头信息
		HashMap var40 = MXKit.getInstance().getCustomHeaders(var6); 
        String var17;
        if (var40 != null && var40.size() > 0) {
            Set var42 = var40.entrySet();
            Iterator var43 = var42.iterator();

            while(var43.hasNext()) {
                Entry var16 = (Entry)var43.next();
                var17 = (String)var16.getKey();
                String var18 = (String)var16.getValue();
                ((HttpUriRequest)var28).addHeader(var17, var18);
            }
        }

		HttpResponse var45 = var9.execute((HttpUriRequest)var28); //此处，发起HTTP请求，并返回响应结果
        this.errorCode = var45.getStatusLine().getStatusCode(); // 将本次请求的响应状态吗赋值给 this.errorCode 成员变量	

		//以下代码，将本次http请求的响应实体数据，转成字符串，赋值给 var8 变量
		InputStream var46 = var45.getEntity().getContent();
        ByteArrayOutputStream var49 = new ByteArrayOutputStream();
        byte[] var50 = new byte[8192];
        boolean var51 = true;

        int var52;
        while((var52 = var46.read(var50)) != -1) {
            var49.write(var50, 0, var52);
        }

		var8 = new String(var49.toByteArray());

		//是否需要将 响应头信息，封装到Json字符串中，作为响应数据一起返回
		if (var1.isAjax()) {
            JSONObject var53 = new JSONObject();

            var53.put("status", this.errorCode);

            var53.put("data", var8);

            Header[] var20 = var45.getAllHeaders();
            JSONObject var21 = new JSONObject();
            Header[] var22 = var20;
            int var23 = var20.length;

            for(int var24 = 0; var24 < var23; ++var24) {
                Header var25 = var22[var24];
                var21.put(var25.getName(), var25.getValue());
            }

            var53.put("headers", var21);

            var8 = var53.toJSONString();
        }

		//返回响应实体数据（可能包含响应头信息）的Json格式字符串
        return var8;

	}