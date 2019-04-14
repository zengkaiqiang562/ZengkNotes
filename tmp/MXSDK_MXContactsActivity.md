## MXContactsActivity ##
此界面是通讯录界面

	protected void onCreate(Bundle var1) {

		this.setContentView(layout.mx_tab_contacts);

        this.contactManager = MXUIEngine.getInstance().getContactManager();
        this.contactManager.a(this);

		this.handleIntentData();
        this.findViewById();
        this.initHeaderView();
        this.fillContent();
	}

	/*从敏行窗口，点击发起群聊进入MXContactsActivity时，
		(new Builder()).setMode(101).setDeptSelectAble(true).setJudgeImPermission(true).setSelectDeptContainChild(true)*/
	private void handleIntentData() {

        this.params = (ContactsParams)this.getIntent().getSerializableExtra("mxkit_contact_params");
        if (this.params == null) {
            this.params = (new Builder()).setMode(100).setStartDeptID(-1).setPersonInfo(200).build(this);
        }
    }

	private void findViewById() {
        this.customHeader = (RelativeLayout)this.findViewById(id.custom_header);
        this.systemHeader = (RelativeLayout)this.findViewById(id.default_header);

        this.headerBack = (ImageButton)this.findViewById(id.title_left_button); //左上角的 "<" 图标，在default_header容器中

        this.backRoot = (ImageButton)this.findViewById(id.title_to_root); //左上角的"X"图标，在default_header容器中

		//右上角的 带矩形框的"√" 图标，表示全选。在default_header容器中
		//在敏行界面点击发起群聊菜单进入此通讯录界面选择参与的群聊人员时，会显示此全选图标
        this.allSelectedBtn = (ImageButton)this.findViewById(id.select_all_btn);  

        this.searchBtn = (LinearLayout)this.findViewById(id.search_btn);  //HeadView 下面的关于搜索的LinearLayout容器，通常不显示

        this.searchLayout = (LinearLayout)this.findViewById(id.mx_search_layout); //this.searchBtn容器中包含的一个LinearLayout容器

        this.title = (TextView)this.findViewById(id.title);  //custom_header 和 default_header容器中都包含同样的 id.title，表示标题，通常为"通讯录"

        this.mxSearchBtn = (ImageButton)this.findViewById(id.mx_search_btn); //default_header中右上角的搜索图标

        this.contactContent = (LinearLayout)this.findViewById(id.mx_contact_content);  //显示通讯录内容，在代码中动态添加内容，
    }

	private void initHeaderView() {
        if (this.params.getMode() == 100 && this.params.getStartDeptID() == -1) {
			//隐藏默认HeadView，显示自定义HeadView

            this.customHeader.setVisibility(0);
            this.systemHeader.setVisibility(8);

			//在ClientTabActivity中的initContactsHeaderView()方法内
			//调用contactManager.setHeaderView(contactsHeader)设置自定义HeadView
            View var1 = this.contactManager.getHeaderView();

            if (var1 != null && this.customHeader.getChildCount() < 1) {

                this.customHeader.removeAllViews();

                var1 = this.contactManager.getHeaderView();

                LayoutParams var2 = new LayoutParams(-1, -2);
                if (var1.getParent() != null) {
                    ((ViewGroup)var1.getParent()).removeView(var1);
                }

                this.customHeader.addView(var1, var2);
            } else {
                this.customHeader.setVisibility(8);
                this.systemHeader.setVisibility(0);
            }

			//右上角不显示全选按钮
            this.allSelectedBtn.setVisibility(8);

        } else {//从敏行界面右上角菜单中的发起群聊进入时，this.params.getMode() == 101

			//隐藏自定义HeadView，显示默认HeadView
            this.customHeader.setVisibility(8);
            this.systemHeader.setVisibility(0);
            this.title.setText(this.params.getHeadTitle());
            this.backRoot.setVisibility(8);

			//判读是否在右上角显示全选按钮，从敏行界面右上角菜单中的发起群聊进入时，会显示全选按钮
            if (this.params.getMode() == 101 && this.params.getAtMost() == 0) {
                this.allSelectedBtn.setVisibility(0);
            } else {
                this.allSelectedBtn.setVisibility(8);
            }
        }

    }

	private void fillContent() {
		//根据this.params选择contactView的类型，
		//params.getMode为100时对应NormalContactView， （底部导航栏“通讯录”进入）
		//101对应MultiChoiceContactView，(敏行界面右上角的发起群聊菜单进入)
		//102对应SingleChoiceContactView
        this.contactView = a.me().a(this, this.params);

        this.contactView.setParams(this.params);
        this.contactView.initView();

        this.contactView.cr(this.params.getStartDeptID()); //最终调用contactView.cs(final int var1)初始化通信录内容，并调用contactView.N(true)刷新

        this.contactContent.removeAllViews();
        this.contactContent.addView(this.contactView, new android.widget.RelativeLayout.LayoutParams(-1, -1));

        this.contactView.mx(); //监听广播“com.minxing.refresh.contact”，收到广播时会调用BaseContactView.this.mt();初始化通讯录内容this.aiX
    }


## mx_tab_contacts.xml ##

	<RelativeLayout
        android:id="@+id/custom_header"
        android:layout_width="fill_parent"
        android:layout_height="@dimen/mx_title_bar_height"
        android:background="@color/mx_title_bar_color" >
    </RelativeLayout>

    <include
        android:id="@+id/default_header"
        layout="@layout/mx_contacts_header_view" />


## com.minxing.kit.ui.contacts.a.java ##

	public BaseContactView a(Context var1, ContactsParams var2) {
        if (var2.getMode() == 100) {
            return new NormalContactView(var1);
        } else if (var2.getMode() == 101) {
            return new MultiChoiceContactView(var1);
        } else {
            return var2.getMode() == 102 ? new SingleChoiceContactView(var1) : null;
        }
    }

## com.minxing.kit.internal.contact.view.BaseContactView.java ##

	public class BaseContactView extends RelativeLayout implements IXListViewListener

	protected Stack<ContactsPO> aiX = new Stack(); // ContactsPO中保存了集合List<IContact>

	public BaseContactView(Context var1) {
        super(var1);
        this.mContext = var1;

        this.contactManager = MXUIEngine.getInstance().getContactManager();
    }

	public void initView() {
		
		View var1 = View.inflate(this.mContext, layout.mx_contacts_screen, (ViewGroup)null);

		this.aiW = (TextView)var1.findViewById(id.deptTitle);

        this.aiQ = (XListView)var1.findViewById(id.mx_contact_list);
		this.aiQ.setXListViewListener(this);

		this.ahn = (LinearLayout)var1.findViewById(id.index_bar); //右侧的索引条

		this.aht = (TextView)var1.findViewById(id.show_head_toast_text); //滑动索引条时，在中间显示的当前索引值
        this.ahs = (LinearLayout)var1.findViewById(id.index); //显示当前索引值的控件this.aht 的容器

        
	}

	protected void cs(final int var1) {
		/* 
			初始化 this.aiX集合，
			a. 可能直接从personal_contact_list数据表中查到当前用户的通讯录内容
			b. 也可能通过MXInterface.SYNC_PERSONAL_CONTACT_FROM_SERVER（/api/v1/subscriptions/users）从网络查找当前用户的通讯录内容，
				然后在更新到personal_contact_list数据表中
		*/
		
		this.N(true); //刷新通讯录内容
	}

	protected void N(boolean var1) {

		List var2 = ((ContactsPO)this.aiX.peek()).getList();

        int var3 = ((ContactsPO)this.aiX.peek()).getDepartID();
        String var4 = ((ContactsPO)this.aiX.peek()).getDeptFullName();

		//根据是否存在通讯录内容显示ListView 或“no data”提示
        if (var2.isEmpty()) {
            this.nodata.setVisibility(0);
            this.aiQ.setVisibility(8);
        } else {
            this.nodata.setVisibility(8);
            this.aiQ.setVisibility(0);
        }

		//this.agu是ListView的适配器
		if (this.agu == null) {
			//初始化适配器
            this.agu = new ei(var2, this.mContext, 
								this.params.getMode(), this.params.getPersonInfo(), 
								this.params.isDeptSelectAble(), this.params.getStartDeptID(), 
								this.params.getSelectedPersons());

            this.agu.setParams(this.params);
            this.aiQ.setAdapter(this.agu);
        } else {
			//刷新适配器中的通讯录内容数据
            this.agu.F(var2);
            this.agu.notifyDataSetChanged();
        }

	}

## com.minxing.colorpicker.ei ##
展示通讯录内容的ListView的适配器

	public class ei extends BaseAdapter

	private List<IContact> aif;

	public ei(List<IContact> var1, Context var2, int var3, int var4, boolean var5, int var6, List<String> var7) {
        this.mContext = var2;

        this.aif = var1;

        this.mode = var3; //ContactsParams.getMode()
        this.personInfo = var4;  //ContactsParams.getPersonInfo()
        this.aig = var5;  //ContactsParams.isDeptSelectAble()
        this.aii = var6;  //ContactsParams.getStartDeptID()
        this.aij = var7;  //可能是null

        this.currentUserID = df.iA().iB().getCurrentIdentity().getId();

        DiskCacheUtils.removeFromCache(Scheme.DRAWABLE.wrap(drawable.mx_icon_department + ""), ImageLoader.getInstance().getDiskCache());
    }

	public int getItemViewType(int var1) {
        switch(null.zZ[((IContact)this.aif.get(var1)).getContactType().ordinal()]) {

	        case 1: //ContactType.PEOPLE
	            return 0;
	        case 2: //ContactType.DEPARTMENT
	            return 1;

		...
	}

	public View getView(int var1, View var2, final ViewGroup var3) {
	
		IContact var4 = (IContact)this.aif.get(var1);

		this.aih = new ei.a(); //this.aih 是ViewHolder	
	
		switch(null.zZ[var4.getContactType().ordinal()]) {
            case 1:
                var2 = LayoutInflater.from(this.mContext).inflate(layout.mx_cmcontact_person_item, (ViewGroup)null);
                this.aih.avatar = (ImageView)var2.findViewById(id.avatar); //左侧头像图标
                this.aih.FY = (ImageView)var2.findViewById(id.selectIcon); //右侧选择图标
                this.aih.air = (ImageView)var2.findViewById(id.email_icon); //邮箱图标
                this.aih.ais = (ImageView)var2.findViewById(id.sms_icon); //两朵云的图标
                this.aih.ait = (ImageView)var2.findViewById(id.call_icon); //右侧电话图标
                this.aih.aiu = (ImageView)var2.findViewById(id.chat_icon); //右侧聊天图标（一朵云）
                this.aih.aiv = (TextView)var2.findViewById(id.cell_num);  //头像图标右下方的电话号码
                this.aih.tv = (TextView)var2.findViewById(id.name); //头像图标右上方的名字
                this.aih.aid = (TextView)var2.findViewById(id.index_label);
                this.aih.FX = (TextView)var2.findViewById(id.department);
                this.aih.aiw = (TextView)var2.findViewById(id.mail_address);
                this.aih.aiA = var2.findViewById(id.mx_contacts_avatar_cover);
                this.aih.aiz = (ImageView)var2.findViewById(id.mx_contacts_status_icon); //名字右边的电话图标
                var2.setTag(this.aih);
                break;
			case 2:
                var2 = LayoutInflater.from(this.mContext).inflate(layout.mx_cmcontact_dept_item, (ViewGroup)null);
                this.aih.tv = (TextView)var2.findViewById(id.name);
                this.aih.avatar = (ImageView)var2.findViewById(id.avatar);
                this.aih.aiy = (ImageView)var2.findViewById(id.arrow_right);
                this.aih.FY = (ImageView)var2.findViewById(id.selectIcon);
                this.aih.FY.setTag(drawable.mx_icon_checkbox_normal);
                this.aih.aix = (ImageView)var2.findViewById(id.selectIconSingle);
                this.aih.aix.setTag(drawable.mx_radio_button_unchecked);
                var2.setTag(this.aih);
                break;
		}
	}
