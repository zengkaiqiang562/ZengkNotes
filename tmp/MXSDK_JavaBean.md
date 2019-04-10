### IContact.java ###

	package com.minxing.kit.internal.common.bean.contact;
	public interface IContact

	IContact.ContactType getContactType();

    int getParentDeptID();

    public static enum ContactType {
        PEOPLE,
        DEPARTMENT,
        OPTION_SERVICE,
        OPTION_COMPANY,
        OPTION_RECENT,
        OPTION_MULTICHAT,
        OPTION_AT,
        OPTION_VIP,
        OPTION_CUSTOM;

        private ContactType() {}
    }

### ContactDepartment.java ###

	package com.minxing.kit.internal.common.bean.contact;
	public class ContactDepartment implements IContact, Serializable

    private ContactType mContactType;
    private int id;
    private int dept_id;
    private String dept_code;
    private String short_name;
    private String full_name;
    private int level;
    private String parent_dept_code;
    private int status;
    private String created_at;
    private String updated_at;
    private int network_id;
    private String short_pinyin;
    private String full_pinyin;
    private int parent_dept_id;
    private int display_order;

	public ContactDepartment(ContactType var1) {
        this.mContactType = var1;
    }

### ContactPeople.java ###

	package com.minxing.kit.internal.common.bean.contact;
	public class ContactPeople implements IContact, Serializable

	/*  {
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
      }  */

				/*	ContactPeople var10 = new ContactPeople(ContactType.PEOPLE);
                    var10.setPerson_id(var6.getIntValue("id"));
                    var10.setPerson_name(var6.getString("name"));
                    var10.setAccount_id(var6.getIntValue("account_id"));
                    var10.setRole_code(var6.getIntValue("role_code"));
                    var10.setAvatar_url(var6.getString("avatar_url"));
                    var10.setShort_pinyin(var6.getString("short_pinyin"));
                    var10.setPinyin(var6.getString("pinyin"));
                    var10.setMobile_number(var6.getString("cellvoice1"));
                    var10.setWork_number(var6.getString("workvoice"));
                    var10.setDept_name(var6.getString("dept_name"));
                    var10.setEmail(var6.getString("email"));
                    var10.setLogin_name(var6.getString("login_name"));
                    var10.setCall_phones(var6.getString("call_phones"));
                    var10.setEmail_exts(var6.getString("email_exts"));
                    var10.setAdmin(var6.getBooleanValue("admin"));
                    var10.setPermission(var6.getIntValue("permission"));
                    var10.setDept_code(var6.getString("dept_code"));
                    var10.setTitle(var6.getString("title"));

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
				*/

	private ContactType mContactType;
    private int id;
    private int person_id; //"id"
    private int account_id; //"account_id"
    private String person_name; //"name"
    private String mobile_number; //"cellvoice1"
    private String work_number; //"workvoice"
    private String preferred_mobile; 
    private String pinyin; //"pinyin"
    private String short_pinyin; //"short_pinyin"
    private int role_code; //"role_code"
    private String avatar_url; //"avatar_url"
    private String dept_name; //"dept_name"
    private String position;
    private int dept_id;  //"dept_id"
    private String dept_code; //"dept_code"
    private int display_order;
    private int network_id;
    private String created_at;
    private String updated_at;
    private int hidden;
    private String email; //"email"
    private String emp_code;
    private String login_name; //"login_name"
    private String call_phones = ""; //"call_phones"
    private String email_exts = "";  //"email_exts"
    private boolean auto_subscribed;
    private boolean admin; //"admin"
    private int permission; //"permission"
    private String online; //"online"
    private String title; //"title"
	
	public ContactPeople(ContactType var1) {
        this.mContactType = var1;
    }

### WBPersonExtension.java ###

{
  "id":111,
  "name":"贺安筠",
  "account_id":111,
  "network_id":2,
  "pinyin":"heanjun",
  "hidden":false,
  "user_code":"001002010:010",
  "dept_code":"001002010",
  "dept_id":15,
  "actived":true,
  "suspended":0,
  "short_pinyin":"haj",
  "role_code":1,
  "root_domain_id":2,
  "login_name":"heanjun",
  "real_name":null,
  "id_number":null,
  "real_name_approved":false,
  "dept_ref_id":"2018073015332594461bcae",
  "conversations_feed_id":761,
  "api_url":"/api/v1/users/111",
  "web_url":"#users/111",
  "avatar_url":"/photos/user_photo_1",
  "account_channel":"/u/TUtZgKlT4aJIIK6xMJioHQ",
  "email":"abc@163.com",
  "feedback_channel":"/s",
  "uploaded_photo":false,
  "join_time":"2019-01-17T13:50:12+08:00",
  "joined_domains":[],
  "default_domain_id":null,
  "admin":null,
  "organization_admin":false,
  "is_a_admin_about_domain":false,
  "is_a_domain_admin":false,
  "is_a_ocu_admin":false,
  "is_a_app_admin":false,
  "is_only_ocu_admin":false,
  "ocu_admin":false,
  "online":"offline",
  "type":"user",
  
  "stats":{"followers":0,"followings":3},
  
  "user_info":{
  	"account_info":{"email":"abc@163.com","language":"zh-CN","time_zone":"Asia/Shanghai"},
  	"department":"教学单位/马克思主义学院",
  	"title":"副教授",
  	"signature":null,
  	"experience":null,
  	"skills":null,
  	"interests":null,
  	"emp_code":"",
  	"cellvoice1":"12312341234",
  	"cellvoice2":null,
  	"workvoice":null,
  	"extension":null,
  	"workfax":null,
  	"preferred_mobile":null,
  	"position":null
  },
  
  "title":"副教授",
  
  "user_show":[
  	{"column":"avatar","name":"头像","revisable":true,"dial":false,"value":"/photos/user_photo_1"},
  	
  	{"column":"qrcode","name":"我的二维码","revisable":false,"dial":false,"value":"/qr_add_friend/get_qrcode"},
  	
  	{"column":"emp_code","name":"工号","revisable":false,"dial":false,"value":""},
  	
  	{"column":"name","name":"姓名","revisable":true,"dial":false,"value":"贺安筠"},
  	
  	{"column":"department","name":"部门","revisable":false,"dial":false,"value":"教学单位/马克思主义学院"},
  	
  	{"column":"title","name":"职务","revisable":false,"dial":false,"value":"副教授"},
  	
  	{"column":"cellvoice1","name":"手机号","revisable":true,"dial":true,"value":"12312341234"},
  	
  	{"column":"workvoice","name":"工作电话","revisable":true,"dial":true,"value":""},
  	
  	{"column":"email","name":"邮箱","revisable":false,"dial":false,"value":"abc@163.com"},
  	
  	{"column":"position","name":"位置","revisable":true,"dial":false,"value":""},
  	
  	{"column":"id_number","name":"身份证号","revisable":true,"dial":false,"value":""}
  ],
  
  "call_phones":"12312341234",
  "permission":7,
  "is_followed_by":false,
  "export_chat":false,
  "enable_actions":true,
  "dept_name":"教学单位/马克思主义学院"
}

	private int id;
    private String name;
    private String nickname;
    private String api_url;
    private String web_url;
    private String avatar_url;
    private String department;
    private String title;
    private String signature;
    private String experience;
    private String skills;
    private String interests;
    private String cellvoice1;
    private String cellvoice2;
    private String workvoice;
    private String extension;
    private String email;
    private String workfax;
    private boolean is_followed_by;
    private String pinyin;
    private String short_pinyin;
    private String preferred_mobile;
    private String emp_code;
    private int role_code;
    private boolean auto_subscribed;
    private boolean actived;
    private String login_name;
    private int dept_id;
    private String dept_code;

    private List<UserDetailShow> userShow;

    private String call_phones = "";
    private String email_exts = "";
    private boolean enable_actions;
    private int permission;


### UserDetailShow.java ###
column值不同，其他key的值也不同

{
"column":"avatar",
"name":"头像",
"revisable":true,
"dial":false,
"value":"/photos/user_photo_1"
}

    private String column;
    private String name;
    private boolean revisable;
    private boolean dial;
    private String value;
    private boolean isDeleted;