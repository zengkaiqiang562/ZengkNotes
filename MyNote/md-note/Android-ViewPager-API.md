## android.support.v4.view.ViewPager ##

	public class ViewPager extends ViewGroup

	private static final int DEFAULT_OFFSCREEN_PAGES = 1;
    private static final int MAX_SETTLE_DURATION = 600; // ms
    private static final int MIN_DISTANCE_FOR_FLING = 25; // dips
    private static final int DEFAULT_GUTTER_SIZE = 16; // dips
    private static final int MIN_FLING_VELOCITY = 400; // dips

	private final ArrayList<ItemInfo> mItems = new ArrayList<ItemInfo>();
		//ViewPager容器中的page页面元素封装在ItemInfo实例中，集合mItems中保存了ViewPager中的
			所有page页面元素

	PagerAdapter mAdapter;
    int mCurItem;   // Index of currently displayed page.

	public ViewPager(Context context)

	public void setAdapter(PagerAdapter adapter)
	public PagerAdapter getAdapter()

	public void addOnAdapterChangeListener(@NonNull OnAdapterChangeListener listener)
	public void removeOnAdapterChangeListener(@NonNull OnAdapterChangeListener listener)

	public void addOnPageChangeListener(OnPageChangeListener listener)
	public void removeOnPageChangeListener(OnPageChangeListener listener)

	public void setPageTransformer(boolean reverseDrawingOrder, 
			PageTransformer transformer) {

        setPageTransformer(reverseDrawingOrder, transformer, View.LAYER_TYPE_HARDWARE);
    }

	public void setPageTransformer(boolean reverseDrawingOrder, 
			PageTransformer transformer, int pageLayerType)
		//参数pageLayerType可取值： View.LAYER_TYPE_HARDWARE|LAYER_TYPE_SOFTWARE
										|LAYER_TYPE_NONE

	public void setCurrentItem(int item) {
        mPopulatePending = false;
        setCurrentItemInternal(item, !mFirstLayout, false);
    }

	public void setCurrentItem(int item, boolean smoothScroll) {
        mPopulatePending = false;
        setCurrentItemInternal(item, smoothScroll, false);
    }

	public int getOffscreenPageLimit()

	public void setOffscreenPageLimit(int limit) {
        if (limit < DEFAULT_OFFSCREEN_PAGES) {
            Log.w(TAG, "Requested offscreen page limit " + limit + " too small; 
					defaulting to " + DEFAULT_OFFSCREEN_PAGES);
            limit = DEFAULT_OFFSCREEN_PAGES;
        }
        if (limit != mOffscreenPageLimit) {
            mOffscreenPageLimit = limit;
            populate();
        }
    }

	public void setPageMargin(int marginPixels)
	public int getPageMargin()

	public void setPageMarginDrawable(Drawable d)
	public void setPageMarginDrawable(@DrawableRes int resId)

	public boolean beginFakeDrag()
	
	public void fakeDragBy(float xOffset)

	public void endFakeDrag()

	public boolean isFakeDragging()

### android.support.v4.view.ViewPager.OnPageChangeListener ###

	public interface OnPageChangeListener

	void onPageScrolled(int position, float positionOffset, int positionOffsetPixels);

	void onPageSelected(int position);

	void onPageScrollStateChanged(int state);
		//参数state可取值：ViewPager.SCROLL_STATE_IDLE|SCROLL_STATE_DRAGGING
									|SCROLL_STATE_SETTLING

### android.support.v4.view.ViewPager.SimpleOnPageChangeListener ###

	public static class SimpleOnPageChangeListener implements OnPageChangeListener

	@Override
    public void onPageScrolled(int position, float positionOffset, 
		int positionOffsetPixels) {}

    @Override
    public void onPageSelected(int position) {}

    @Override
    public void onPageScrollStateChanged(int state) {}

### android.support.v4.view.ViewPager.PageTransformer ###

	public interface PageTransformer

	void transformPage(View page, float position);

### android.support.v4.view.ViewPager.OnAdapterChangeListener ###

	public interface OnAdapterChangeListener

	void onAdapterChanged(@NonNull ViewPager viewPager,
                @Nullable PagerAdapter oldAdapter, @Nullable PagerAdapter newAdapter);

## android.support.v4.view.PagerAdapter ##

	public abstract class PagerAdapter

	public static final int POSITION_UNCHANGED = -1;
    public static final int POSITION_NONE = -2;

	public abstract int getCount();

	public abstract boolean isViewFromObject(View view, Object object);
		//此方法用于判断view是否跟object关联，view来自于ViewPager容器视图中已添加的子View，
			object来自于instantiateItem方法的返回值，在ViewPager中，instantiateItem方法返回
			的代表一个页面的object实例被存储在ArrayList<ItemInfo>类型的集合mItems中。
		当我们要在集合mItems中查找跟view关联的page页面信息时，就会遍历这个mItems集合，取出每个
		ItemInfo中的object，然后调用PagerAdapter的isViewFromObject方法，在方法内部判断参数view
		是否跟参数object关联，关联的话就返回true，否则返回false。
		通常instantiateItem方法返回的object要么是一个View，要么是一个Fragment。当是一个View时，
		判断"view == object"即可；当是一个Fragment时，那么view就是Fragment的根视图，
		判断"((Fragment)object).getView() == view"即可
			

	public void startUpdate(ViewGroup container) {
        startUpdate((View) container);
    }
		//在ViewPager中调用PagerAdapter的instantiateItem或destroyItem或setPrimaryItem方法
			之前，会先调用tartUpdate方法，表示开始要对ViewPager的当前页面或缓存页面进行更新了。
			参数container即ViewPager
	
	@Deprecated
    public void startUpdate(View container) {}

	public Object instantiateItem(ViewGroup container, int position) {
        return instantiateItem((View) container, position);
    }	
		//如果ViewPager要在position位置处创建一个新页面，那么调用此方法表示创建并初始化一个
			新的page页面，然后把page页面的根视图添加到container容器中
			参数container表示需要创建页面的ViewPager对象
			此方法会被调用多次，参数position可能是当前显示页面的位置，也可能是缓存页面的位置。
			返回一个代表此新建page页面的容器对象，一般是page页面的根视图

	@Deprecated
    public Object instantiateItem(View container, int position) {
        throw new UnsupportedOperationException(
                "Required method instantiateItem was not overridden");
    }

	public void destroyItem(ViewGroup container, int position, Object object) {
        destroyItem((View) container, position, object);
    }
		//如果ViewPager中的position位置处的object所表示的页面，不是当前显示的页面，也不是缓存页面，
			那么调用此方法表示删除position位置处object所表示的页面，即在此方法中将页面根视图View
			从container视图容器中移除

	@Deprecated
    public void destroyItem(View container, int position, Object object) {
        throw new UnsupportedOperationException("Required method destroyItem was not overridden");
    }

	public void setPrimaryItem(ViewGroup container, int position, Object object) {
        setPrimaryItem((View) container, position, object);
    }
		//当ViewPager中的position位置处的page页面作为当前页面显示时调用此方法。
			参数object代码position位置处的page页面，object可能是View，也可能是Fragment

	@Deprecated
    public void setPrimaryItem(View container, int position, Object object) {}

	public void finishUpdate(ViewGroup container) {
        finishUpdate((View) container);
    }
		//在ViewPager中调用PagerAdapter的instantiateItem或destroyItem或setPrimaryItem方法
			之后，会调用finishUpdate方法，表示已完成对ViewPager的当前页面或缓存页面进行的更新操作。
			参数container即ViewPager
			在FragmentPagerAdater中，会在此方法内提交事务。因为在执行此方法之前调用的
			instantiateItem或destroyItem方法中，都是对Fragment进行的一次事务操作过程（如将
			Fragment添加到ViewPager、或从ViewPager中删除Fragment）

	@Deprecated
    public void finishUpdate(View container) {}

	public int getItemPosition(Object object) {
        return POSITION_UNCHANGED;
    }

	public void notifyDataSetChanged() {
        synchronized (this) {
            if (mViewPagerObserver != null) {
                mViewPagerObserver.onChanged();
            }
        }
        mObservable.notifyChanged();
    }

	public void registerDataSetObserver(DataSetObserver observer) {
        mObservable.registerObserver(observer);
    }

	public void unregisterDataSetObserver(DataSetObserver observer) {
        mObservable.unregisterObserver(observer);
    }

	public CharSequence getPageTitle(int position) {
        return null;
    }

	public float getPageWidth(int position) {
        return 1.f;
    }

## android.support.v4.app.FragmentPagerAdapter ##

	public abstract class FragmentPagerAdapter extends PagerAdapter

	public FragmentPagerAdapter(FragmentManager fm) {
        mFragmentManager = fm;
    }

	public abstract Fragment getItem(int position);

	public long getItemId(int position) {
        return position;
    }

	@Override
    public Object instantiateItem(ViewGroup container, int position) {
        if (mCurTransaction == null) {
            mCurTransaction = mFragmentManager.beginTransaction();
        }

        final long itemId = getItemId(position);

        // Do we already have this fragment?
        String name = makeFragmentName(container.getId(), itemId);

        Fragment fragment = mFragmentManager.findFragmentByTag(name);

        if (fragment != null) {

            if (DEBUG) Log.v(TAG, "Attaching item #" + itemId + ": f=" + fragment);
            mCurTransaction.attach(fragment);

        } else {
            fragment = getItem(position);
            if (DEBUG) Log.v(TAG, "Adding item #" + itemId + ": f=" + fragment);

            mCurTransaction.add(container.getId(), fragment,
                    makeFragmentName(container.getId(), itemId));
        }
        if (fragment != mCurrentPrimaryItem) {
            fragment.setMenuVisibility(false);
            fragment.setUserVisibleHint(false);
        }

        return fragment;
    }

	@Override
    public void destroyItem(ViewGroup container, int position, Object object) {
        if (mCurTransaction == null) {
            mCurTransaction = mFragmentManager.beginTransaction();
        }

        if (DEBUG) Log.v(TAG, "Detaching item #" + getItemId(position) + ": f=" + object
            	+ " v=" + ((Fragment)object).getView());
            
        mCurTransaction.detach((Fragment)object);
    }

	@Override
    public void setPrimaryItem(ViewGroup container, int position, Object object) {
        Fragment fragment = (Fragment)object;
        if (fragment != mCurrentPrimaryItem) {
            if (mCurrentPrimaryItem != null) {
                mCurrentPrimaryItem.setMenuVisibility(false);
                mCurrentPrimaryItem.setUserVisibleHint(false);
            }
            if (fragment != null) {
                fragment.setMenuVisibility(true);
                fragment.setUserVisibleHint(true);
            }
            mCurrentPrimaryItem = fragment;
        }
    }

	@Override
    public void finishUpdate(ViewGroup container) {
        if (mCurTransaction != null) {
            mCurTransaction.commitNowAllowingStateLoss();
            mCurTransaction = null;
        }
    }

    @Override
    public boolean isViewFromObject(View view, Object object) {
        return ((Fragment)object).getView() == view;
    }

    private static String makeFragmentName(int viewId, long id) {
        return "android:switcher:" + viewId + ":" + id;
    }
