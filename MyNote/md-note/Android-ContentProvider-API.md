## android.content.ContentProvider ##

	public abstract class ContentProvider implements ComponentCallbacks2

	public abstract boolean onCreate();

	public abstract @Nullable Uri insert(@NonNull Uri uri, 
			@Nullable ContentValues values);

	public abstract int delete(@NonNull Uri uri, 
			@Nullable String selection, @Nullable String[] selectionArgs);

	public abstract int update(@NonNull Uri uri, 
			@Nullable ContentValues values,
            @Nullable String selection, @Nullable String[] selectionArgs);

	public abstract @Nullable Cursor query(@NonNull Uri uri, 
			@Nullable String[] projection,
            @Nullable String selection, @Nullable String[] selectionArgs,
            @Nullable String sortOrder);

	public abstract @Nullable String getType(@NonNull Uri uri);
		//返回MIME Type类型，没有则返回null

	public void shutdown() {
        Log.w(TAG, "implement ContentProvider shutdown() to make sure all database " +
                "connections are gracefully shutdown");
    }

## android.net.Uri ##

	public abstract class Uri implements Parcelable, Comparable<Uri>

	public static Uri parse(String uriString)

	public static Uri fromFile(File file)

	public abstract String getScheme();

	public abstract String getAuthority();

	public abstract String getPath();

## android.content.ContentResolver ##

	public abstract class ContentResolver

	public final @Nullable Uri insert(@RequiresPermission.Write @NonNull Uri url,
            @Nullable ContentValues values)

	public final int delete(@RequiresPermission.Write @NonNull Uri url, 
			@Nullable String where,  @Nullable String[] selectionArgs)

	public final int update(@RequiresPermission.Write @NonNull Uri uri,
            @Nullable ContentValues values, 
			@Nullable String where, @Nullable String[] selectionArgs)

	public final @Nullable Cursor query(@RequiresPermission.Read @NonNull Uri uri,
            @Nullable String[] projection, 
			@Nullable String selection, @Nullable String[] selectionArgs, 
			@Nullable String sortOrder)

	public final @Nullable String getType(@NonNull Uri url)

	--------注册ContentObserver监听器 ----------

	public final void registerContentObserver(@NonNull Uri uri, 
			boolean notifyForDescendants,
            @NonNull ContentObserver observer)
		//参数uri指定监听器observer所监听的ContentProvider的Uri
		//参数notifyForDescendants如果设为true，那么假如监听的Uri为content://abc，则
			"content://abc/xyz"，"content://abc/xyz/foo"等表示的Uri也会被监听；
			如果notifyForDescendants设为false，那么就只监听参数uri。

	public final void unregisterContentObserver(@NonNull ContentObserver observer)

	--------通知监听器ContentProvider的数据变化情况-----

	public void notifyChange(@NonNull Uri uri, @Nullable ContentObserver observer)
		//此方法一般在ContentProvider中的query、insert、update、delete方法内操作完数据后调用，
			参数observer设为null，表示所有在ContentResolver中注册的监听器ContentObserver
			都会监听到。

	public void notifyChange(@NonNull Uri uri, @Nullable ContentObserver observer,
            @NotifyFlags int flags)

## android.content.UriMatcher ##

	public class UriMatcher

	public static final int NO_MATCH = -1;

	public UriMatcher(int code) //参数code设为UriMatcher.NO_MATCH即可

	public void addURI(String authority, String path, int code) 
		//authority和path组合成一个Uri（这个Uri的scheme固定是content://）。参数code是这个
			被组合成的Uri的唯一标识。Uri不同，code值的定义也要不同，但code值必须大于0

	public int match(Uri uri)
		//将参数Uri和在UriMatcher中添加了的Uri进行匹配，如果匹配成功，则返回添加此Uri时设置的
			code标识码，如果匹配失败，则返回-1。

## android.content.ContentUris ##

	public class ContentUris

	public static long parseId(Uri contentUri)
		//当Uri由content://authority/path/id组成时，返回id表示的某条记录在path数据表中的行号标记
		//如果Uri的最后一个路径分隔符"/"后面不是数值id，那么会抛出异常

	public static Uri withAppendedId(Uri contentUri, long id)
		//在Uri最后添加id，返回一个添加了id的Uri。
		//通常参数contentUri应该是content://authority/path 这种不带数值id的形式。

## android.database.ContentObserver ##

	public abstract class ContentObserver 

	public ContentObserver(Handler handler) {
        mHandler = handler;
    }

	public void onChange(boolean selfChange, Uri uri) {
        onChange(selfChange);
    }

	public void onChange(boolean selfChange) {
        // Do nothing.  Subclass should override.
    }
	------------源码分析----------------
	private static final class Transport extends IContentObserver.Stub {
        ...
        @Override
        public void onChange(boolean selfChange, Uri uri, int userId) {
            ContentObserver contentObserver = mContentObserver;
            if (contentObserver != null) {
                contentObserver.dispatchChange(selfChange, uri, userId);
            }
        }
        ...
    }
	private void dispatchChange(boolean selfChange, Uri uri, int userId) {
        if (mHandler == null) {
            onChange(selfChange, uri, userId);
        } else {
            mHandler.post(new NotificationRunnable(selfChange, uri, userId));
        }
    }

	private final class NotificationRunnable implements Runnable {
        ...
        @Override
        public void run() {
            ContentObserver.this.onChange(mSelfChange, mUri, mUserId);
        }
    }

	//@hide
	public void onChange(boolean selfChange, Uri uri, int userId) {
        onChange(selfChange, uri);
    }