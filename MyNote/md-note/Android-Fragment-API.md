## android.support.v4.app.Fragment ##

	public class Fragment implements ComponentCallbacks, OnCreateContextMenuListener

	-------------------lifecycle methods start----------------------------

    public void onAttach(Context context) {
        mCalled = true;
        final Activity hostActivity = mHost == null ? null : mHost.getActivity();
        if (hostActivity != null) {
            mCalled = false;
            onAttach(hostActivity);
        }
    }

	@Deprecated
	public void onAttach(Activity activity)

	public void onCreate(@Nullable Bundle savedInstanceState)

	public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container,
            @Nullable Bundle savedInstanceState)
		//重写此方法为Fragment片段提供布局，返回的View是Fragment布局的根视图
		//如果继承的是一个ListFragment，那么此方法默认返回一个ListView
		//调用参数inflater的inflate(int resource, ViewGroup root, boolean attachToRoot)方法
			即可为Fragment创建布局，其中root参数一般设置为container，表示inflate方法返回的Fragment
			布局的根视图将插入到container父容器中。参数attachToRoot设为false即可。

	public void onViewCreated(View view, @Nullable Bundle savedInstanceState) {}
		//当onCreateView执行完成，并返回一个非null的View对象时被调用

	public void onActivityCreated(@Nullable Bundle savedInstanceState)

	public void onStart()

	public void onResume()

	public void onPause()

	public void onStop()

	public void onDestroyView()

	public void onDestroy()

	public void onDetach()

	-------------------lifecycle methods end----------------------------
	public void onCreateOptionsMenu(Menu menu, MenuInflater inflater
	
	public void setHasOptionsMenu(boolean hasMenu)
		//设置为true，Fragment中的onCreateOptionsMenu方法才会被回调
	
	public void registerForContextMenu(View view)
	public void unregisterForContextMenu(View view)


## android.support.v4.app.FragmentActivity ##

	public FragmentManager getSupportFragmentManager()

## android.support.v4.app.FragmentManager ##

	public abstract class FragmentManager

	public abstract FragmentTransaction beginTransaction();

	public abstract Fragment findFragmentById(int id);

	public abstract Fragment findFragmentByTag(String tag);

	public abstract void popBackStack(); //将片段从返回栈中弹出（模拟用户发出的返回命令）

	public abstract void addOnBackStackChangedListener(	 //监听返回栈的变化
			OnBackStackChangedListener listener);

### android.app.FragmentManager.OnBackStackChangedListener ###

	public interface OnBackStackChangedListener

	public void onBackStackChanged();

## android.support.v4.app.FragmentTransaction ##

	public abstract class FragmentTransaction	

	public abstract FragmentTransaction add(Fragment fragment, String tag);

	public abstract FragmentTransaction add(@IdRes int containerViewId, 
			Fragment fragment);

	public abstract FragmentTransaction add(@IdRes int containerViewId, 
			Fragment fragment, @Nullable String tag)

	public abstract FragmentTransaction replace(@IdRes int containerViewId, 
			Fragment fragment);

	public abstract FragmentTransaction replace(@IdRes int containerViewId, 
			Fragment fragment, @Nullable String tag);

	public abstract FragmentTransaction remove(Fragment fragment);

	public abstract FragmentTransaction hide(Fragment fragment);
	public abstract FragmentTransaction show(Fragment fragment);

	public abstract FragmentTransaction addToBackStack(@Nullable String name);
		//一般在commit方法之前调用此方法，表示将本次事务中执行的操作，添加到返回栈中。
			当调用FragmentManager的popBackStack()时，会将最后一次添加到返回栈中的事务操作移除。
		//参数name设为null即可。

	public abstract boolean isAddToBackStackAllowed();

	public abstract FragmentTransaction setTransition(@Transit int transit);
		//设置事务的过渡动画，参数transit的值可以是：TRANSIT_NONE|TRANSIT_FRAGMENT_OPEN
			|TRANSIT_FRAGMENT_CLOSE|TRANSIT_FRAGMENT_FADE

	public abstract int commit();

	public abstract int commitAllowingStateLoss();

	