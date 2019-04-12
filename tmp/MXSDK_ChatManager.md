## ChatManager.java ##

#### queryConversationList 方法 ####

	public List<Conversation> queryConversationList(Context var1) {
		...//从数据表conversation_list中查询当前用户的所有聊天记录的集合 List<Conversation>
	}


#### startNewChat方法 ####
点击发起群聊调用此方法

	public void startNewChat(Activity var1) {
        Object var2 = null;
		
		//HardcodeChatActivity在MXChatActivity的onCreate方法中被设置为MXChatActivity对象
        MXChatActivity var3 = MXUIEngine.getInstance().getChatManager().getHardcodeChatActivity();
        if (var3 != null) {
            var2 = var3;
        } else if (this.mChatActivity != null) {
            var2 = this.mChatActivity;
        } else {
            var2 = var1;
        }

        ContactsParams var4 = (new Builder()).setMode(101).setDeptSelectAble(true)
						.setJudgeImPermission(true).setSelectDeptContainChild(true).build((Context)var2);

		//此处var2就是MXChatActivity
        ContactIntentAdapter.getInstance().startContactActivityForResult((Activity)var2, var4, 1001);
    }


## ContactIntentAdapter ##

	//发起群聊时，此处var1就是MXChatActivity
	public void startContactActivityForResult(Activity var1, ContactsParams var2, int var3) {
        if (!this.handleConfigurationContact(var1, var2, var3)) {
            Intent var4 = new Intent(var1, MXContactsActivity.class);

            var4.putExtra("mxkit_contact_params", var2);

            var1.startActivityForResult(var4, var3);
        }
    }