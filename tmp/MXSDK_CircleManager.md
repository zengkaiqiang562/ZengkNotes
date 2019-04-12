## CircleManager.java ##

	
#### shareToCircle方法 （工作分享） ####

	public void shareToCircle(Activity var1) {
        Object var2 = null;
        if (this.customActivity == null) {
            if (this.mCircleActivity != null) {
                var2 = this.mCircleActivity;
            } else {
                var2 = var1;
            }
        } else {
            var2 = this.customActivity;
        }

        Intent var3 = new Intent((Context)var2, NewMessageTextActivity.class);

        var3.putExtra("is_need_send_contact", true);
        var3.putExtra("SELECTED_ITEM_NAME", var1.getString(string.mx_top_right_circle_message));
        var3.putExtra("unReadRefresh", true);

        ((Activity)var2).startActivityForResult(var3, 0);
    }