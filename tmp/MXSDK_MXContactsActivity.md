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

        this.contactContent = (LinearLayout)this.findViewById(id.mx_contact_content);
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