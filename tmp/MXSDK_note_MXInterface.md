	package com.minxing.kit.internal.core;
	public enum MXInterface

	...,
	GET_PERSONAL_INFO("/api/v1/users/%s?include_user_info=true&is_followed_by=%s", HashMap.class), //查询联系人详情

	CONTACT_SEARCH("/api/v1/departments/search", Object.class), //搜索联系人

	SYNC_PERSONAL_CONTACT_FROM_SERVER("/api/v1/subscriptions/users", Object.class), //查询通讯录内容

	SYNC_PERSONAL_CONTACT_ALL_FROM_SERVER("/api/v1/subscriptions/users/all_contacts", Object.class), //查询所有通讯录内容，一般不使用

	CONTACT_LIST("/api/v1/departments", Object.class), //查询（公司通讯录）下的部门联系人列表

	OCUS_SUBSCRIBED("/api/v1/subscriptions/ocus", Object.class), //查询当前用户的所有公众号

	CONVERSATION_QUERY_BY_OCU("/api/v1/conversations/about_ocu/%d", Object.class), //查询当前用户跟某个公众号的聊天记录信息

	OCUS_UNSUBSCRIBED("/api/v1/ocus/unsubscribed?ocu_type=1", Object.class), //查询未订阅的公众号

	APPSTORE_APPS_MINE("/api/v1/apps/added", Object.class), //获取 我的应用界面中 展示的应用，可以带的查询参数（include_category， include_hidden_apps）

	//点击 我的应用界面中 文件助手应用图标时请求此url，其中的 %s 替换成 file_helper。 返回的json数据封装成 ConversationOCUOwner
	OCUS_DETAIL_BY_OCU_ID("/api/v1/ocus/by_ocu_id/%s", Object.class), 

	APPSTORE_ALL_APPS("/api/v1/apps/full", Object.class), //进入 AllAppsActivity界面（全部应用）时，从此url地址获取所有应用，
	...;

	private String mInterface;
    private String formatFace;
    private Class<?> mClazz;

	private MXInterface(String var3, Class<?> var4) {
        this.mInterface = var3;
        this.formatFace = var3;
        this.mClazz = var4;
    }

	public String getFormatFace() { return this.formatFace; }

    public String getInsType() { return this.mInterface; }

    public Class<?> getClazz() { return this.mClazz; }

	public MXInterface insertParam(Object... var1) {
        if (var1 != null) {
            this.formatFace = String.format(this.mInterface, var1);
        } else {
            this.formatFace = this.mInterface;
        }

        return this;
    }