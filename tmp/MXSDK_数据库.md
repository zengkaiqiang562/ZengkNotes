## dn.java ##
此类操作数据库 CONVERSATION_CIPHERED.db

	public class dn

    private static dm QG = null; //new dm(mContext);
    private static dn QH = null;

	private static SQLiteDatabase QI = null;  //QI = QG.B(mContext);

    private static final String QJ = "conversation_list";
    private static final String QK = "message_list";
    private static final String QL = "ocu_info_list";
    private static final String QM = "personal_contact_list";
    private static final String QN = "vip_contact_list";
    private static final String QO = "mail_contact_list";
    private static final String QP = "appstore_app_list";
    private static final String QR = "missing_message";
    private static final String QS = "message_marker_list";

	//通过参数WBPersonExtension， 更新 personal_contact_list 表中的指定联系人的信息
	public int b(WBPersonExtension var1) {
        dn var2 = QH;
        synchronized(QH) {
            ContentValues var3 = new ContentValues();
            var3.put("person_name", var1.getName());
            var3.put("mobile_number", var1.getCellvoice1());
            var3.put("preferred_mobile", var1.getPreferred_mobile());
            var3.put("pinyin", var1.getPinyin());
            var3.put("short_pinyin", var1.getShort_pinyin());
            var3.put("avatar_url", var1.getAvatar_urlForDB());
            var3.put("department_name", var1.getDepartment());
            var3.put("email", var1.getEmail());
            var3.put("work_number", var1.getWorkvoice());
            var3.put("call_phones", var1.getCall_phones());
            var3.put("email_exts", var1.getEmail_exts());
            var3.put("permission", var1.getPermission());
            var3.put("EXT_1", var1.getTitle());
            return this.update("personal_contact_list", var3, "person_id = ?", new String[]{String.valueOf(var1.getId())});
        }
    }

	//更新 vip_contact_list 表中 指定联系人的信息，vip_contact_list表应该是特别关注的联系人表
	public int c(WBPersonExtension var1) {
        dn var2 = QH;
        synchronized(QH) {
            ContentValues var3 = new ContentValues();
            var3.put("person_name", var1.getName());
            var3.put("mobile_number", var1.getCellvoice1());
            var3.put("preferred_mobile", var1.getPreferred_mobile());
            var3.put("pinyin", var1.getPinyin());
            var3.put("short_pinyin", var1.getShort_pinyin());
            var3.put("avatar_url", var1.getAvatar_urlForDB());
            var3.put("department_name", var1.getDepartment());
            var3.put("email", var1.getEmail());
            var3.put("work_number", var1.getWorkvoice());
            var3.put("call_phones", var1.getCall_phones());
            var3.put("email_exts", var1.getEmail_exts());
            return this.update("vip_contact_list", var3, "person_id = ?", new String[]{String.valueOf(var1.getId())});
        }
    }

	public Conversation a(int var1, int var2, String var3, boolean var4) {
        dn var5 = QH;
        synchronized(QH) {
            String var6 = Boolean.toString(var4);
            StringBuffer var7 = new StringBuffer();
            var7.append("and graph_id");
            if (var3 != null && !"".equals(var3)) {
                var7.append(" = " + var3);
            } else {
                var7.append(" is null");
            }

            Conversation var8 = null;

            Cursor var9 = this.rawQuery("SELECT * FROM conversation_list 
							+ where current_user_id = ? and interlocutor_user_ids = ? 
							+ and state <> ? and conversation_id <> ? 
							+ and multi_user == ?" + var7.toString() 
							+ " ORDER BY update_at DESC", 
					new String[]{String.valueOf(var2), String.valueOf(var1),
					String.valueOf(2), "-9991", var6});
					
            if (var9.moveToFirst()) {
                var8 = new Conversation();
                this.a(var8, var9);
            }

            var9.close();
            return var8;
        }
    }

### dm.java ###
此类中定义数据库 CONVERSATION_CIPHERED.db

数据库：
	CONVERSATION_CIPHERED.db

CONVERSATION_CIPHERED.db中的表

	conversation_list

	message_list

	personal_contact_list

	ocu_info_list

	mail_contact_list

	appstore_app_list

	vip_contact_list

	init_flag

源码：

	public class dm

	private static dm.a QF;

	public dm(Context var1) {
        ...
                QF = new dm.a(var1);
        ...
    }

	private static class a extends dl { //dl 继承自 net.sqlcipher.database.SQLiteOpenHelper

		public a(Context var1) {

            super(var1, "CONVERSATION_CIPHERED.db", (CursorFactory)null, 64100000);
        }

		public void onCreate(SQLiteDatabase var1) {
			//创建表
		}

		public void onUpgrade(SQLiteDatabase var1, int var2, int var3) {
			//更新表
		}
		
	}

## di.java ## 
操作数据库 LOCALCACHE_CIPHERED.db

源码：

	private static dh Qv = null;  //new dh(mContext);
    private static SQLiteDatabase mDatabase = null; //Qv.B(mContext);

	//将WBPersonExtension中部分 跟联系人相关的数据 写入到 cache_person_list 表中
	public void a(WBPersonExtension var1) {
        CachePerson var2 = new CachePerson();
        var2.setPersonID(var1.getId());
        var2.setName(var1.getName());
        var2.setPinyin(var1.getPinyin());
        var2.setShort_pinyin(var1.getShort_pinyin());
        var2.setAvatar_url(var1.getAvatar_urlForDB());
        var2.setLogin_name(var1.getLogin_name());

        this.b(var2); //将 CachePerson中的数据插入到 cache_person_list 表中
    }

	

### dh.java ###
定义数据库 

	LOCALCACHE_CIPHERED.db

LOCALCACHE_CIPHERED.db 中的表有： 

	cache_person_list
	
	init_flag


源码：

	private static dh.a Qu;

	public dh(Context var1) {
        ...
                Qu = new dh.a(var1);
        ...
    }

	private static class a extends dl { //dl 继承自 net.sqlcipher.database.SQLiteOpenHelper

		public a(Context var1) {
            super(var1, "LOCALCACHE_CIPHERED.db", (CursorFactory)null, 2);
        }

		public void onCreate(SQLiteDatabase var1) {
			//创建表
		}

		public void onUpgrade(SQLiteDatabase var1, int var2, int var3) {
			//更新表
		}

	}

### CachePerson.java ###

    private int personID;
    private String name;
    private String pinyin;
    private String short_pinyin;
    private String avatar_url;
    private String login_name;
    private int role_code;
