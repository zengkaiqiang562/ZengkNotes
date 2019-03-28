## android.database.sqlite.SQLiteClosable ##

	public abstract class SQLiteClosable implements Closeable

	public void close()

## android.database.sqlite.SQLiteDatabase ##

	public final class SQLiteDatabase extends SQLiteClosable

**创建或删除数据库文件**

	public static SQLiteDatabase openOrCreateDatabase(File file, CursorFactory factory) {
        return openOrCreateDatabase(file.getPath(), factory);
    }
    
    public static SQLiteDatabase openOrCreateDatabase(String path, CursorFactory factory) {
        return openDatabase(path, factory, CREATE_IF_NECESSARY, null);
    }
    
    public static SQLiteDatabase openOrCreateDatabase(String path, CursorFactory factory,
            DatabaseErrorHandler errorHandler) {
        return openDatabase(path, factory, CREATE_IF_NECESSARY, errorHandler);
    }

	public static SQLiteDatabase openDatabase(String path, CursorFactory factory, 
			int flags) {

        return openDatabase(path, factory, flags, null);
    }

	public static SQLiteDatabase openDatabase(String path, CursorFactory factory, 
			int flags, DatabaseErrorHandler errorHandler) {

        SQLiteDatabase db = new SQLiteDatabase(path, flags, factory, errorHandler);
        db.open();
        return db;
    }

	public static boolean deleteDatabase(File file)  //返回true表示成功删除数据库文件file

**操作数据库**

	public void execSQL(String sql) throws SQLException

	public void execSQL(String sql, Object[] bindArgs) throws SQLException

	public long insert(String table, String nullColumnHack, ContentValues values)

	public int update(String table, ContentValues values, 
			String whereClause, String[] whereArgs)

	public int delete(String table, String whereClause, String[] whereArgs)

	public Cursor rawQuery(String sql, String[] selectionArgs)

	public Cursor query(boolean distinct, 
			String table, String[] columns,
            String selection, String[] selectionArgs, 
			String groupBy, String having, 
			String orderBy, 
			String limit)

	public Cursor query(
			String table, String[] columns, 
			String selection,String[] selectionArgs, 
			String groupBy, String having,
            String orderBy) {
		
		return query(false, 
				table, columns, 
				selection, selectionArgs, 
				groupBy, having, 
				orderBy, 
				null /* limit */);
	}

	public Cursor query(
			String table, String[] columns, 
			String selection, String[] selectionArgs, 
			String groupBy, String having,
            String orderBy, 
			String limit) {

		return query(false, 
				table, columns, 
				selection, selectionArgs, 
				groupBy, having, 
				orderBy, 
				limit);
	}

	--------执控事务的API------------

	public void beginTransaction()

	public void setTransactionSuccessful()

	public void endTransaction()
	
	-----------其他 API--------------------
	public int getVersion() //返回当前数据库的版本号
	public void setVersion(int version) //为当前数据库设置版本号，没设置之前，默认为0

## android.database.Cursor ##

	public interface Cursor extends Closeable

	int getCount();

	int getPosition();

	boolean move(int offset);

	boolean moveToPosition(int position);

	boolean moveToFirst();
	boolean moveToLast();

	boolean moveToNext();
	boolean moveToPrevious();

	boolean isFirst();
	boolean isLast();

	boolean isBeforeFirst();
	boolean isAfterLast();

	int getColumnIndex(String columnName); 
		//当参数columnName指定的字段名，在当前数据表中不存在时，返回 -1
		//返回值表示参数字段在当前数据表中的索引值∈[0, count - 1]

	int getColumnIndexOrThrow(String columnName) throws IllegalArgumentException;
		//当参数columnName指定的字段名，在当前数据表中不存在时，抛出异常

	String getColumnName(int columnIndex);

	String[] getColumnNames();

	int getColumnCount();

	short getShort(int columnIndex);
	int getInt(int columnIndex);
	long getLong(int columnIndex);

	float getFloat(int columnIndex);
	double getDouble(int columnIndex);

	String getString(int columnIndex);

	byte[] getBlob(int columnIndex);

	int getType(int columnIndex);
		//返回 Cursor.FIELD_TYPE_NULL|FIELD_TYPE_INTEGER|FIELD_TYPE_FLOA
				|FIELD_TYPE_STRING|FIELD_TYPE_BLOB

	boolean isNull(int columnIndex);

	void close();

	boolean isClosed();

## android.content.ContentValues ##

	public final class ContentValues implements Parcelable

	private HashMap<String, Object> mValues;

	public ContentValues() {
        mValues = new HashMap<String, Object>(8);
    }

	public ContentValues(int size) {
        mValues = new HashMap<String, Object>(size, 1.0f);
    }

	public ContentValues(ContentValues from) {
        mValues = new HashMap<String, Object>(from.mValues);
    }

	//Xxx表示类型，可以是String|Byte|Short|Integer|Long|Float|Double|Boolean|byte[]
	public void put(String key, Xxx value) {
        mValues.put(key, value);
    }

	public void putAll(ContentValues other) {
        mValues.putAll(other.mValues);
    }

	public void putNull(String key) {
        mValues.put(key, null);
    }

	public int size() {
        return mValues.size();
    }

    public boolean isEmpty() {
        return mValues.isEmpty();
    }

	public void remove(String key) {
        mValues.remove(key);
    }

    public void clear() {
        mValues.clear();
    }

	public boolean containsKey(String key) {
        return mValues.containsKey(key);
    }

	public Object get(String key) {
        return mValues.get(key);
    }

	//Xxx表示类型，可以是String|Byte|Short|Integer|Long|Float|Double|Boolean|ByteArray
	public String getAsXxx(String key)

	public Set<Map.Entry<String, Object>> valueSet() {
        return mValues.entrySet();
    }

    public Set<String> keySet() {
        return mValues.keySet();
    }

## android.database.sqlite.SQLiteOpenHelper ##

	public abstract class SQLiteOpenHelper

	public SQLiteOpenHelper(Context context, String name, CursorFactory factory, 
			int version) {
        this(context, name, factory, version, null);
    }

	public SQLiteOpenHelper(Context context, String name, CursorFactory factory, 
			int version, DatabaseErrorHandler errorHandler)

	//@hide
	public SQLiteOpenHelper(Context context, String name, CursorFactory factory, 
		int version, int minimumSupportedVersion, DatabaseErrorHandler errorHandler) {

        if (version < 1) 
			throw new IllegalArgumentException("Version must be >= 1, was " + version);

        mContext = context;
        mName = name;
        mFactory = factory;
        mNewVersion = version;
        mErrorHandler = errorHandler;
        mMinimumSupportedVersion = Math.max(0, minimumSupportedVersion);
    }

	public String getDatabaseName() {//返回构造方法中参数name指定的数据库名称
        return mName;
    }

	public SQLiteDatabase getWritableDatabase() {
        synchronized (this) {
            return getDatabaseLocked(true);
        }
    }

	public SQLiteDatabase getReadableDatabase() {
        synchronized (this) {
            return getDatabaseLocked(false);
        }
    }

	-----------源码分析 start-------------
	private SQLiteDatabase mDatabase;
	private final int mNewVersion;
	private final String mName;
	private boolean mIsInitializing;
	private static final boolean DEBUG_STRICT_READONLY = false;

	private SQLiteDatabase getDatabaseLocked(boolean writable) {
        if (mDatabase != null) {
            if (!mDatabase.isOpen()) {
                // Darn!  The user closed the database by calling mDatabase.close().
                mDatabase = null;
            } else if (!writable || !mDatabase.isReadOnly()) {
                // The database is already open for business.
                return mDatabase;
            }
        }

        if (mIsInitializing) {
            throw new IllegalStateException("getDatabase called recursively");
        }

        SQLiteDatabase db = mDatabase;
        try {
            mIsInitializing = true;

            if (db != null) {
                if (writable && db.isReadOnly()) {
                    db.reopenReadWrite();
                }
            } else if (mName == null) {
                db = SQLiteDatabase.create(null);
            } else {
                try {
                    if (DEBUG_STRICT_READONLY && !writable) {
                        final String path = mContext.getDatabasePath(mName).getPath();
                        db = SQLiteDatabase.openDatabase(path, mFactory,
                                SQLiteDatabase.OPEN_READONLY, mErrorHandler);
                    } else {
                        db = mContext.openOrCreateDatabase(mName, mEnableWriteAheadLogging ?
                                Context.MODE_ENABLE_WRITE_AHEAD_LOGGING : 0,
                                mFactory, mErrorHandler);
                    }
                } catch (SQLiteException ex) {
                    if (writable) {
                        throw ex;
                    }
                    Log.e(TAG, "Couldn't open " + mName
                        	+ " for writing (will try read-only):", ex);
                    final String path = mContext.getDatabasePath(mName).getPath();
                    db = SQLiteDatabase.openDatabase(path, mFactory,
                            SQLiteDatabase.OPEN_READONLY, mErrorHandler);
                }
            }

            onConfigure(db);

            final int version = db.getVersion();
            if (version != mNewVersion) {
                if (db.isReadOnly()) {
                    throw new SQLiteException("Can't upgrade read-only database from 
					version " + db.getVersion() + " to " + mNewVersion + ": " + mName);
                }

                if (version > 0 && version < mMinimumSupportedVersion) {
					//通常不会执行到这里，因为mMinimumSupportedVersion默认为0，一般不会再修改
                    File databaseFile = new File(db.getPath());
                    onBeforeDelete(db);
                    db.close();
                    if (SQLiteDatabase.deleteDatabase(databaseFile)) {
                        mIsInitializing = false;
                        return getDatabaseLocked(writable);
                    } else {
                        throw new IllegalStateException("Unable to delete obsolete database " + mName + " with version " + version);
                    }
                } else {
                    db.beginTransaction();
                    try {
                        if (version == 0) {
                            onCreate(db);
                        } else {
                            if (version > mNewVersion) {
                                onDowngrade(db, version, mNewVersion);
                            } else {
                                onUpgrade(db, version, mNewVersion);
                            }
                        }
                        db.setVersion(mNewVersion);
                        db.setTransactionSuccessful();
                    } finally {
                        db.endTransaction();
                    }
                }
            }

            onOpen(db);

            if (db.isReadOnly()) {
                Log.w(TAG, "Opened " + mName + " in read-only mode");
            }

            mDatabase = db;
            return db;
        } finally {
            mIsInitializing = false;
            if (db != null && db != mDatabase) {
                db.close();
            }
        }
    }
	-----------源码分析 end---------------

	public synchronized void close() {
        if (mIsInitializing) 
			throw new IllegalStateException("Closed during initialization");

        if (mDatabase != null && mDatabase.isOpen()) {
            mDatabase.close();
            mDatabase = null;
        }
    }

	public void onConfigure(SQLiteDatabase db) {}
		//调用此方法，说明一个之前没有打开过的数据库，已经被打开了。

	public void onBeforeDelete(SQLiteDatabase db) {}
		//此回调方法一般不会被执行。只有当已存在的数据库版本小于最小支持的版本时，才会在删除这个已
			存在的数据库之前调用此方法。但是最小支持版本默认为0，而数据库的版本也不会小于0，所以此
			方法不会被调用。

	public abstract void onCreate(SQLiteDatabase db);
		//当名为mName的数据库被打开后（如果不存在会先创建再打开），判断此数据库的版本，如果版本号
			为0，那么会回调此onCreate方法。只有在第一次打开此数据库时，版本号才是0。因为使用
			SQLiteOpenHelper第一次打开数据库后，会为这个数据库设置一个大于0的版本号。
		//当回调了onCreate方法时，说明此SQLiteOpenHelper对象操作的数据库是第一次被打开，数据库中
			并没有任何数据表，所以我们可以在onCreate方法中，通过参数db的execSQL方法创建一个
			数据表。即在onCreate方法中实现数据库的初始化操作。

	public abstract void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion);
		//当回调了此方法时，说明SQLiteOpenHelper对象中传入的数据库版本号大于这个打开了的
			已存在的数据库db的版本号。当大于时，说明我们要对已存在的数据库进行更新。于是可在onUpgrade方法中完成对数据库db的更新操作。

	public void onDowngrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        throw new SQLiteException("Can't downgrade database from version " +
                oldVersion + " to " + newVersion);
    }
		//当回调了此方法时，说明SQLiteOpenHelper对象中传入的数据库版本号小于这个打开了的已存在的
			数据库db的版本号。这种情况一般是不允许出现的，所以默认抛出异常。但是，也可以重写此方法，
			对已存在的数据库进行版本回退。

	public void onOpen(SQLiteDatabase db) {}
		//当使用SQLiteOpenHelper对象打开了数据库，并完成了对数据库的初始化或更新操作后（只有当
			传入的数据库版本号不等于已存在数据库的版本号时才会发生），回调此onOpen方法。