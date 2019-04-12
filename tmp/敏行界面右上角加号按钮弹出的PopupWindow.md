## SystemMainTopRightPopMenu ##

	package com.minxing.client.widget;

	public class SystemMainTopRightPopMenu extends PopupWindow

	public SystemMainTopRightPopMenu(Context context) {
		super(context);
		mContext = context;

		chatManager = MXUIEngine.getInstance().getChatManager();
		contentView = LayoutInflater.from(mContext).inflate(R.layout.system_top_right_menu, null);


		//发起群聊，跳转到MXContactsActivity界面找准备群聊的联系人
		btn_new_conversation.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				chatManager.startNewChat((Activity) mContext);
			}
		});

		//添加联系人，跳转到AddPersonalContactActivity界面
		btn_add_contact.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				contactManager.addContacts((Activity) mContext);
				
			}
		});

		//扫一扫，跳转到CaptureActivity（将动作"native://showScan"）
		btn_scan.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				MXKit.getInstance().startScan((Activity) mContext);
			}
		});

		//工作分享，跳转到NewMessageTextActivity界面，编辑要分享的内容
		btn_scan.setOnClickListener(new OnClickListener() {
			@Override
			public void onClick(View v) {
				CircleManager circleManager = MXUIEngine.getInstance().getCircleManager();

				circleManager.shareToCircle((Activity) mContext);
			}
		});

	}