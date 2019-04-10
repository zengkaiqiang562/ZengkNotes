## ConversationActivity.java ##

	private gc apJ;  //gc extends BaseAdapter

	public void onCreate(Bundle var1) {

		this.setContentView(layout.mx_conversation_message_list);

        this.LK = this.findViewById(com.minxing.kit.R.id.black_place_holder);

        this.apD = (ConversationListView)this.findViewById(com.minxing.kit.R.id.listview);

		this.apl = (LinearLayout)this.findViewById(com.minxing.kit.R.id.view_header);
        this.backButton = (ImageButton)this.findViewById(com.minxing.kit.R.id.title_left_button);
        this.apq = (MXButton)this.findViewById(com.minxing.kit.R.id.title_right_save_button);
        this.apo = (MXButton)this.findViewById(com.minxing.kit.R.id.btn_mx_system_title_cancel);

		this.apm = (TextView)this.findViewById(com.minxing.kit.R.id.title_name);
        this.apn = (TextView)this.findViewById(com.minxing.kit.R.id.title_secret);
        this.apy = (ImageButton)this.findViewById(com.minxing.kit.R.id.right_btn_check_person_detail);
        this.apz = (ImageButton)this.findViewById(com.minxing.kit.R.id.right_btn_check_person_multi_detail);
        this.apA = (ImageButton)this.findViewById(com.minxing.kit.R.id.right_btn_check_person_ocu_detail);

	}