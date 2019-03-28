# Intent 相关 #

## android.app.PendingIntent ##

	public final class PendingIntent implements Parcelable

	public static PendingIntent getActivity(Context context, int requestCode,
            Intent intent, @Flags int flags) //requestCoe maybe 0, flags maybe 0

	public static PendingIntent getActivity(Context context, int requestCode,
            @NonNull Intent intent, @Flags int flags, @Nullable Bundle options)

	public static PendingIntent getBroadcast(Context context, int requestCode,
            Intent intent, @Flags int flags)

	public static PendingIntent getService(Context context, int requestCode,
            @NonNull Intent intent, @Flags int flags)

	public static PendingIntent getForegroundService(Context context, int requestCode,
            @NonNull Intent intent, @Flags int flags)


## android.content.Intent ##

	public class Intent implements Parcelable, Cloneable

	public Intent(String action, Uri uri)

# users-permission 权限 #
	操作闪光灯的权限
	<uses-permission android:name="android.permission.FLASHLIGHT"/>

	操作振动器的权限
	<uses-permission android:name="android.permission.VIBRATE"/>

# Activity #

## android.app.Activity ##

	public class Activity extends ContextThemeWrapper
        implements LayoutInflater.Factory2,
        Window.Callback, KeyEvent.Callback,
        OnCreateContextMenuListener, ComponentCallbacks2,
        Window.OnWindowDismissedCallback, WindowControllerCallback,
        AutofillManager.AutofillClient

	public LayoutInflater getLayoutInflater()

	public boolean onCreateOptionsMenu(Menu menu) //重写此方法，设置选项菜单

	public boolean onOptionsItemSelected(MenuItem item)

	public void onCreateContextMenu(ContextMenu menu, View v, ContextMenuInfo menuInfo)
		//重写此方法，设置上下文菜单

	public void registerForContextMenu(View view) //给view注册上下文菜单，长按view可弹出上下文菜单

	public void registerForContextMenu(View view) {
        view.setOnCreateContextMenuListener(this);
    }

	public void unregisterForContextMenu(View view) {
        view.setOnCreateContextMenuListener(null);
    }

	public ActionBar getActionBar() //如果主题设置成Xxx.NoActionBar，则返回null

	public void setActionBar(@Nullable Toolbar toolbar)

	public final boolean requestWindowFeature(int featureId) {
        return getWindow().requestFeature(featureId);
    }

	public FragmentManager getFragmentManager()

	public void overridePendingTransition(int enterAnim, int exitAnim)
		//实现Activity的切换动画，enterAnim表示入场动画，exitAnim表示出场动画。
			动画是指Animation补间动画的xml文件。设为0表示不使用动画。作为入场动画时，要在
			startActivity之后调用，作为出场动画时，要在finish()之后调用。

## android.support.v4.app.FragmentActivity ##

	public class FragmentActivity extends BaseFragmentActivityApi16 implements
        ActivityCompat.OnRequestPermissionsResultCallback,
        ActivityCompat.RequestPermissionsRequestCodeValidator

	public FragmentManager getSupportFragmentManager()

## android.support.v7.app.AppCompatActivity ##

	public class AppCompatActivity extends FragmentActivity implements AppCompatCallback,
        TaskStackBuilder.SupportParentable, ActionBarDrawerToggle.DelegateProvider

	public ActionBar getSupportActionBar()

	public void setSupportActionBar(@Nullable Toolbar toolbar)


	