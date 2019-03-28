## SQLite简介 ##
SQLite只是一个嵌入式的数据库引擎，专门适用于资源有限的设备上适量数据的存取，如手机，Pad等设备。

SQLite并不像Oracle、MySQL数据库那样需要安装、启动服务器进程，SQLite数据库只是一个文件。从本质上看，
SQLite的操作方式只是一种更为便捷的文件操作。

## SQLiteDatabase 和 Cursor ##
Android提供SQLiteDatabase类代表一个数据库（底层就是一个数据库文件）。
SQLiteDatabase提供如下方法创建并打开一个数据库（其实就是创建并打开一个数据库文件：

	public static SQLiteDatabase openDatabase(String path, CursorFactory factory, 
			int flags, DatabaseErrorHandler errorHandler)
		//参数errorHandler通常设为null即可
		//参数flags设置为SQLiteDatabase.CREATE_IF_NECESSARY时表示如果path指定的数据库文件不存在
			时，则创建path表示的数据库文件；如果设置为0，则当不存在数据库文件时不会自动创建
		//参数factory可以设为null，表示采用默认工厂
		//参数path指定要打开的数据库文件

SQLiteDatabase提供如下方法对一个打开的数据文件执行DDL、DML、DQL等操作数据库文件的SQL语句

	public void execSQL(String sql) throws SQLException

	public void execSQL(String sql, Object[] bindArgs) throws SQLException

	public long insert(String table, String nullColumnHack, ContentValues values)
		//参数nullColumnHack用于指定强行插入null值的字段名。当values参数为null或values中不包含
			任何key-value键值对时，参数nullColumnHack才有作用
		//返回新添加的记录所在的行号，此行号与主键id无关（但通常等于"_id"主键字段在这条记录上的数
			值），插入记录失败则返回-1
		//不管是否能从values参数获取到预插入的字段数据，inset方法总是会添加一条记录，如果不能从
			values参数中获取到字段数据，那么除作为主键的字段外，其他字段在这条记录上的数据为null，
			此时，插入null的其他字段在nullColumnHack参数中指定。一般来说，nullColumn参数中不能
			有作为主键的字段名，以及设置为NOTNULL(非空)的字段名，否则当强行往这些字段中插入null会
			产生异常

	public int update(String table, ContentValues values, 
			String whereClause, String[] whereArgs)
		//返回被更新的记录的条数
		//如果在whereClause参数表示的where子句中使用了占位符"?"，那么whereArags参数就指定用于
			替代占位符的数据
		//values参数指定要更新的字段(key)，以及此字段的更新后的值(value)

	public int delete(String table, String whereClause, String[] whereArgs)
		//返回被删除的记录的条数。0表示没有删除任何记录，即删除失败

	public Cursor rawQuery(String sql, String[] selectionArgs)

	public Cursor query(boolean distinct,  //是否去除重复记录
			String table, String[] columns,
            String selection, String[] selectionArgs, 
			String groupBy, String having, 
			String orderBy, 
			String limit) //limit参数用于进行分页

	public void beginTransaction()

	public void setTransactionSuccessful()

	public void endTransaction()
	
	public boolean inTransaction() //判断当前是否处于事务中

	public void close() //不使用数据库后，要关闭SQLiteDatabase

执行DQL查询语句后会返回一个Cursor对象用于操作查询到的每条记录。Cursor提供如下方法访问查询结果记录：

	int getCount();
	int getPosition();
	boolean move(int offset);
	boolean moveToPosition(int position);
	boolean moveToFirst();
	boolean moveToLast();
	boolean moveToNext();
	boolean moveToPrevious();

-----------
SQLiteDatabase既代表了与数据库的连接，也可用于执行SQL操作，所以可以把SQLiteDatabase看成JDBC中
Connection和Statement的混合体。
而Android中的Cursor则可当成JDBC中的ResultSet，并且Cursor提供了更多便捷的方法来操作查询结果集

## sqlite3 工具 ##
Android SDK的platform-tools目录下提供了一个sqlite3.ext文件，它是一个简单的SQLite数据库管理工具，
类似于MySQL提供的命令行窗口。

sqlite3的使用方式如下：

	sqlite3 f:/my.db3  //在命名行窗口输入此命令，表示打开数据库文件 my.db3

执行上述命令后，在命令行窗口中会出现`sqlite>`提示符，此时，可使用sqlite3提供的命令操作数据库：

	.databases  //查看当前数据库

	.tables  //查看当前数据库里的数据表

	.help  //查看sqlite3支持的命令

SQLite数据库支持绝大部分常用的SQL语句，可在上述命令行窗口中运行SQL语句。

当Android应用提示某条SQL语句有语法错误时，最好先利用sqlite3这个工具测试这条语句，以保证这条SQL语句
语法的正确性。

## SQLite数据库支持的数据类型 ##
SQLite内部只支持五种数据类型：
	
	NULL
	INTEGER
	REAL     浮点数
	TEXT     文本
	BLOB     大二进制数

但在使用SQL语句创建表时，SQLite完成可以接受 varchar(n)，char(n)，decimal(p,s)等数据类型，只不过
SQLite会在运算或保存时将它们转换为上面5中数据类型中相应的类型。

需要注意的是，SQLite允许把各种类型的数据保存到任意类型的字段中。如程序可以把字符串类型的值存入Integer
类型的字段中，也可以把数值类型的值存入布尔类型的字段中，等等。
特别的，定义为INTEGER PRIMARY KEY的字段只能存储64位整数，不能像此字段插入其他类型的数据，否则会报错。

由于SQLite允许存入数据时忽略底层数据列实际的数据类型，因此在编写建表语句时可以省略数据列后面的类型声明

	create table my_test (
		_id integer primary key autoincrement,
		name,
		pass,
		gender);

	
## SQLiteDatabase中的事务控制 ##

	public void beginTransaction()

	public void setTransactionSuccessful()

	public void endTransaction()
	
	public boolean inTransaction() //判断当前是否处于事务中

通过SQLiteDatabase提供的如上方法可以进行事务控制，其中当执行endTransaction()方法之前，如果调用
了setTransactionSuccessful()方法，那么表示执行endTransaction()方法时会提交事务，否则就会回滚
事务。

	db.beginTransaction();
	try {
		//执行SQL语句
		...
		//调用该方法则表示设置事务成功，于是后续执行endTransaction方法时会提交事务；
		//不调用此方法，那么后续执行endTransaction方法时会回滚事务。
		db.setTransactionSuccessful();
	} finally {
		db.endTransaction();
	}

## SQLiteOpenHelper ##
在实际项目中很少直接调用SQLiteDatabase的方法来打开（或创建）一个数据库，通常会继承
SQLiteOpenHelper类，并在子类中实现SQLiteOpenHelper提供的一些回调方法，如onCreate，onUpgrade等。
通过调用子类对象的getReadableDatabase()或getWritableDatabase()方法来打开数据库。

SQLiteOpenHelper是Android提供的一个管理数据库的工具类，可用于管理数据库的创建和数据库版本的更新。

SQLiteOpenHelper相关的API如下

	public SQLiteOpenHelper(Context context, String name, CursorFactory factory, 
			int version)
		//在子类的构造方法中调用SQLiteOpenHelper类的此构造方法，传入想要操作的数据库的名称和
			数据库的版本

	public SQLiteDatabase getWritableDatabase()
		//已读写的方式打开数据库对应的SQLiteDatabase对象。无法打开时抛出异常。
			当数据库不存在时会在内存创建好并调用SQLiteOpenHelper的onCreate方法初始化完成后，
			再返回数据库对应的SQLiteDatabase对象。
			当数据库的版本号发生改变时，会在内部先调用SQLiteOpenHelper的onUpgrade方法更新完成
			后，再返回数据库对应的SQLiteDatabase对象。

	public SQLiteDatabase getReadableDatabase()
		//已读写的方式打开数据库对应的SQLiteDatabase对象，无法打开时再已只读的方式打开数据库

	public abstract void onCreate(SQLiteDatabase db);
		//当数据库第一次创建并打开时调用，重写此方法可对打开的数据库做初始化工作，如创建一张数据表

	public abstract void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion);
		//当前SQLiteOpenHelper对象传入的版本号大于已打开的数据库的版本号时调用，重写此方法可对
			数据库进行更新。当我们需要更新一张已存在的数据表的字段结构时，可能会因为已有的记录数据
			导致更新升级失败，此时，可能需要在更新之前，将表中的所有记录进行转存备份，
			然后再清空表中的记录，接着再对表进行更新升级，当更新表结构之后，再将备份好的记录重新写入
			表中。

	public synchronized void close()
		//关闭已打开的数据库库。

从SQLiteOpenHelper的源码getDatabaseLocked方法中可以，多次调用同一个SQLiteOpenHelper对象的
getWritableDatabase方法或getReadableDatabase方法时，通常得到的是同一个SQLiteDatabase实例。