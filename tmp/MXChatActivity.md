## MXChatActivity.java ##

	package com.minxing.kit.ui.chat;

	//重写了IXListViewListener的onRefresh()和onLoadMore()方法
	public class MXChatActivity extends BaseActivity implements IXListViewListener

	protected void onCreate(Bundle var1) {

		this.chatManager = MXUIEngine.getInstance().getChatManager();
        this.currentUserInfo = df.iA().iB();

		RelativeLayout var2 = (RelativeLayout)LayoutInflater.from(this).inflate(layout.mx_tab_chat, (ViewGroup)null);
        this.setContentView(var2);

		//将ClientTabActivity的initChatHeaderView()方法中设置的HeadView添加到headerContainer中
		this.headerContainer = (RelativeLayout)var2.findViewById(id.view_header);
        if (this.getHeaderView() != null) {
            this.headerContainer.removeAllViews();
            LayoutParams var3 = new LayoutParams(-1, -2);
            if (this.getHeaderView().getParent() != null) {
                ((ViewGroup)this.getHeaderView().getParent()).removeView(this.getHeaderView());
            }

            this.headerContainer.addView(this.getHeaderView(), var3);
        }

		//ClientTabActivity的initChatHeaderView()中初始化的HeaderView中的搜索按钮会触发此点击事件
		this.chatManager.setSearchClickListener(new OnSearchClickListener() {
            public void onSearchClick() {
                Intent var1 = new Intent(MXChatActivity.this, ConversationSearchActivity.class);
                MXChatActivity.this.startActivity(var1);
            }
        });

		this.xlist = (XListView)var2.findViewById(id.lv_mx_tab_chat);
		this.adapter = new gb(this, this.displayConversationList);
        this.adapter.U(false);
        this.xlist.stopRefresh();
        this.xlist.setAdapter(this.adapter);
        this.xlist.setXListViewListener(this);

	}

	//在ClientTabActivity的initChatHeaderView()方法中会设置HeadView
	protected View getHeaderView() {
        return this.chatManager.getHeaderView();
    }


#### this.loadConversationList 方法 ####
此方法加载聊天记录集合this.displayConversationList
	
	protected void loadConversationList(final boolean var1) {

		final ArrayList var2 = new ArrayList();
		//从ChatManager中查询聊天记录集合
        List var3 = this.chatManager.queryConversationList(this);
        Iterator var4 = var3.iterator();

        while(var4.hasNext()) {
            Conversation var5 = (Conversation)var4.next();
            if (!var5.isSecretChat()) {
                var2.add(var5);
            }
        }

		this.mHandler.post(new Runnable() {
            public void run() {
                MXChatActivity.this.displayConversationList.clear();
                MXChatActivity.this.displayConversationList.addAll(var2);
                MXChatActivity.this.adapter.notifyDataSetChanged();
                MXChatActivity.this.onLoad(var1);
            }
        });

	}


## ListView的适配器：gb.java ##

	public class gb extends BaseAdapter

	private List<Conversation> agM;  

    private String[] ayg; //["file","image","video","voice_file","share_link"]
    private String[] ayh; //["[文件]","[图片]","[视频]","[语音]","[分享]"]
    private Map<String, String> ayi = new HashMap(); //以ayg数组元素为key，ayh数组元素为value

	public gb(Context var1, List<Conversation> var2) {
        this.mContext = var1;

        this.agM = var2;

        this.ayg = this.mContext.getResources().getStringArray(array.mx_conversation_message_type_key);
        this.ayh = this.mContext.getResources().getStringArray(array.mx_conversation_message_type_value);

        for(int var3 = 0; var3 < this.ayg.length; ++var3) {
            this.ayi.put(this.ayg[var3], this.ayh[var3]);
        }

        u.ae(this.mContext);
    }

	public View getView(int var1, View var2, ViewGroup var3) {
			
		Conversation var5 = (Conversation)this.agM.get(var1);

		var2 = LayoutInflater.from(this.mContext).inflate(layout.mx_conversation_list_item, (ViewGroup)null);

	}