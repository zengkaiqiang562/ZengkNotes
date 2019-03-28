## android.view.Window ##

	public abstract void setStatusBarColor(@ColorInt int color);
	public abstract int getStatusBarColor();

### android.view.Window.Callback ###

	public interface Callback

	public boolean dispatchKeyEvent(KeyEvent event);

	public boolean dispatchTouchEvent(MotionEvent event);

	public boolean onMenuItemSelected(int featureId, MenuItem item);

	public void onWindowAttributesChanged(WindowManager.LayoutParams attrs);

	public void onContentChanged();

	public void onAttachedToWindow();
	public void onDetachedFromWindow();

## android.view.View ##

	public class View implements Drawable.Callback, KeyEvent.Callback,
        AccessibilityEventSource

	@attr ref android.R.styleable#View_alpha
	@attr ref android.R.styleable#View_background
	@attr ref android.R.styleable#View_clickable
	@attr ref android.R.styleable#View_contentDescription
	@attr ref android.R.styleable#View_drawingCacheQuality
	@attr ref android.R.styleable#View_duplicateParentState
	@attr ref android.R.styleable#View_id
	@attr ref android.R.styleable#View_requiresFadingEdge
	@attr ref android.R.styleable#View_fadeScrollbars
	@attr ref android.R.styleable#View_fadingEdgeLength
	@attr ref android.R.styleable#View_filterTouchesWhenObscured
	@attr ref android.R.styleable#View_fitsSystemWindows
	@attr ref android.R.styleable#View_isScrollContainer
	@attr ref android.R.styleable#View_focusable
	@attr ref android.R.styleable#View_focusableInTouchMode
	@attr ref android.R.styleable#View_focusedByDefault
	@attr ref android.R.styleable#View_hapticFeedbackEnabled
	@attr ref android.R.styleable#View_keepScreenOn
	@attr ref android.R.styleable#View_keyboardNavigationCluster
	@attr ref android.R.styleable#View_layerType
	@attr ref android.R.styleable#View_layoutDirection
	@attr ref android.R.styleable#View_longClickable
	@attr ref android.R.styleable#View_minHeight
	@attr ref android.R.styleable#View_minWidth
	@attr ref android.R.styleable#View_nextClusterForward
	@attr ref android.R.styleable#View_nextFocusDown
	@attr ref android.R.styleable#View_nextFocusLeft
	@attr ref android.R.styleable#View_nextFocusRight
	@attr ref android.R.styleable#View_nextFocusUp
	@attr ref android.R.styleable#View_onClick
	@attr ref android.R.styleable#View_padding
	@attr ref android.R.styleable#View_paddingHorizontal
	@attr ref android.R.styleable#View_paddingVertical
	@attr ref android.R.styleable#View_paddingBottom
	@attr ref android.R.styleable#View_paddingLeft
	@attr ref android.R.styleable#View_paddingRight
	@attr ref android.R.styleable#View_paddingTop
	@attr ref android.R.styleable#View_paddingStart
	@attr ref android.R.styleable#View_paddingEnd
	@attr ref android.R.styleable#View_saveEnabled
	@attr ref android.R.styleable#View_rotation
	@attr ref android.R.styleable#View_rotationX
	@attr ref android.R.styleable#View_rotationY
	@attr ref android.R.styleable#View_scaleX
	@attr ref android.R.styleable#View_scaleY
	@attr ref android.R.styleable#View_scrollX
	@attr ref android.R.styleable#View_scrollY
	@attr ref android.R.styleable#View_scrollbarSize
	@attr ref android.R.styleable#View_scrollbarStyle
	@attr ref android.R.styleable#View_scrollbars
	@attr ref android.R.styleable#View_scrollbarDefaultDelayBeforeFade
	@attr ref android.R.styleable#View_scrollbarFadeDuration
	@attr ref android.R.styleable#View_scrollbarTrackHorizontal
	@attr ref android.R.styleable#View_scrollbarThumbHorizontal
	@attr ref android.R.styleable#View_scrollbarThumbVertical
	@attr ref android.R.styleable#View_scrollbarTrackVertical
	@attr ref android.R.styleable#View_scrollbarAlwaysDrawHorizontalTrack
	@attr ref android.R.styleable#View_scrollbarAlwaysDrawVerticalTrack
	@attr ref android.R.styleable#View_stateListAnimator
	@attr ref android.R.styleable#View_transitionName
	@attr ref android.R.styleable#View_soundEffectsEnabled
	@attr ref android.R.styleable#View_tag
	@attr ref android.R.styleable#View_textAlignment
	@attr ref android.R.styleable#View_textDirection
	@attr ref android.R.styleable#View_transformPivotX
	@attr ref android.R.styleable#View_transformPivotY
	@attr ref android.R.styleable#View_translationX
	@attr ref android.R.styleable#View_translationY
	@attr ref android.R.styleable#View_translationZ
	@attr ref android.R.styleable#View_visibility
	@attr ref android.R.styleable#View_theme

	public void startAnimation(Animation animation)
	public void clearAnimation()
	public Animation getAnimation()

	public void getLocationOnScreen(@Size(2) int[] outLocation)
	public void getLocationInWindow(@Size(2) int[] outLocation)

	public final int getLeft()
	public final int getRight()
	public final int getTop()
	public final int getBottom()

	public float getX() {
        return mLeft + getTranslationX();
    }

	public float getY() {
        return mTop + getTranslationY();
    }

	public float getTranslationX() {
        return mRenderNode.getTranslationX();
    }

	public float getTranslationY() {
        return mRenderNode.getTranslationY();
    }

	public void scrollTo(int x, int y)
	public void scrollBy(int x, int y) {
        scrollTo(mScrollX + x, mScrollY + y);
    }

	public View getRootView() {
        if (mAttachInfo != null) {
            final View v = mAttachInfo.mRootView;
            if (v != null) {
                return v;
            }
        }

        View parent = this;

        while (parent.mParent != null && parent.mParent instanceof View) {
            parent = (View) parent.mParent;
        }

        return parent;
    }

	public final ViewParent getParent() {
        return mParent;
    }

	public void setOnCreateContextMenuListener(OnCreateContextMenuListener l)

	public interface OnCreateContextMenuListener {...}

### android.view.View.OnCreateContextMenuListener ###

	void onCreateContextMenu(ContextMenu menu, View v, ContextMenuInfo menuInfo);

### android.view.View.MeasureSpec ###

	public static class MeasureSpec

	private static final int MODE_SHIFT = 30;
    private static final int MODE_MASK  = 0x3 << MODE_SHIFT;
		// 1100 0000 0000 0000 0000 0000 0000 0000

	public static final int UNSPECIFIED = 0 << MODE_SHIFT; 
		// 0000 0000 0000 0000 0000 0000 0000 0000
    
	public static final int EXACTLY     = 1 << MODE_SHIFT;
		// 0100 0000 0000 0000 0000 0000 0000 0000
	
	public static final int AT_MOST     = 2 << MODE_SHIFT;
		// 1000 0000 0000 0000 0000 0000 0000 0000

		//(1 << MeasureSpec.MODE_SHIFT) - 1 = 
			0100 0000 0000 0000 0000 0000 0000 0000
		  - 0000 0000 0000 0000 0000 0000 0000 0001
		  = 0011 1111 1111 1111 1111 1111 1111 1111	  
	public static int makeMeasureSpec(
			@IntRange(from = 0, to = (1 << MeasureSpec.MODE_SHIFT) - 1) int size,
			@MeasureSpecMode int mode) {

        if (sUseBrokenMakeMeasureSpec) {
            return size + mode;
        } else {
            return (size & ~MODE_MASK) | (mode & MODE_MASK);
        }
    }

	public static int getMode(int measureSpec) {
        return (measureSpec & MODE_MASK);
    }

	public static int getSize(int measureSpec) {
        return (measureSpec & ~MODE_MASK);
    }

## android.view.ViewGroup ##

	public abstract class ViewGroup extends View implements ViewParent, ViewManager
	
		//是否限制子View的显示范围，true表示限制，此时子View中超出子View布局范围的内容不会显示在
			父容器中；false表示不限制，则子View中超出子View布局范围的内容会显示在父容器中。
	@attr ref android.R.styleable#ViewGroup_clipChildren 
		
	@attr ref android.R.styleable#ViewGroup_clipToPadding
	@attr ref android.R.styleable#ViewGroup_layoutAnimation
	@attr ref android.R.styleable#ViewGroup_animationCache
	@attr ref android.R.styleable#ViewGroup_persistentDrawingCache
	@attr ref android.R.styleable#ViewGroup_alwaysDrawnWithCache
	@attr ref android.R.styleable#ViewGroup_addStatesFromChildren
	@attr ref android.R.styleable#ViewGroup_descendantFocusability
	@attr ref android.R.styleable#ViewGroup_animateLayoutChanges
	@attr ref android.R.styleable#ViewGroup_splitMotionEvents
	@attr ref android.R.styleable#ViewGroup_layoutMode

	public void setLayoutAnimation(LayoutAnimationController controller)

	public void requestDisallowInterceptTouchEvent(boolean disallowIntercept)
		//是否为父控件设置FLAG_DISALLOW_INTERCEPT标记为。disallowIntercept为true，表示设置此
			标记为，即向父控件申请不拦截事件。因为已经确定不拦截，所以此时无需再调用父控件的
			onInterceptTouchEvent方法判断是否拦截。
		//disallowIntercept为true设为false，则父控件清除了对FLAG_DISALLOW_INTERCEPT标记的设置，
			此时，父控件需要通过调用onInterceptTouchEvent方法判断是否拦截事件。

	public static class LayoutParams {...}

## android.view.ViewGroup.LayoutParams ##

	//<declare-styleable name="ViewGroup_Layout">
	@attr ref android.R.styleable#ViewGroup_Layout_layout_height
	@attr ref android.R.styleable#ViewGroup_Layout_layout_width

	public LayoutParams(int width, int height)

	public LayoutParams(Context c, AttributeSet attrs)

	public LayoutParams(LayoutParams source)

## android.view.ViewGroup.MarginLayoutParams ##

	public static class MarginLayoutParams extends ViewGroup.LayoutParams

	//<declare-styleable name="ViewGroup_MarginLayout">
	@attr ref android.R.styleable#ViewGroup_MarginLayout_layout_margin
    @attr ref android.R.styleable#ViewGroup_MarginLayout_layout_marginHorizontal
    @attr ref android.R.styleable#ViewGroup_MarginLayout_layout_marginVertical
    @attr ref android.R.styleable#ViewGroup_MarginLayout_layout_marginLeft
    @attr ref android.R.styleable#ViewGroup_MarginLayout_layout_marginTop
    @attr ref android.R.styleable#ViewGroup_MarginLayout_layout_marginRight
    @attr ref android.R.styleable#ViewGroup_MarginLayout_layout_marginBottom
    @attr ref android.R.styleable#ViewGroup_MarginLayout_layout_marginStart
    @attr ref android.R.styleable#ViewGroup_MarginLayout_layout_marginEnd

	public MarginLayoutParams(Context c, AttributeSet attrs)

	public MarginLayoutParams(int width, int height)

	public MarginLayoutParams(int width, int height)

	public MarginLayoutParams(LayoutParams source)

## android.view.LayoutInflater ##

	@SystemService(Context.LAYOUT_INFLATER_SERVICE)
	public abstract class LayoutInflater

	public static LayoutInflater from(Context context)

		public static LayoutInflater from(Context context) {
	        LayoutInflater LayoutInflater = (LayoutInflater) context
					.getSystemService(Context.LAYOUT_INFLATER_SERVICE);

	        if (LayoutInflater == null) {
	            throw new AssertionError("LayoutInflater not found.");
	        }
	        return LayoutInflater;
	    }

	public View inflate(@LayoutRes int resource, @Nullable ViewGroup root)

## android.view.MenuInflater ##

	public class MenuInflater

	public MenuInflater(Context context)

	public void inflate(@MenuRes int menuRes, Menu menu)

## android.view.MotionEvent ##

	public final class MotionEvent extends InputEvent implements Parcelable
	
	public final int getActionIndex() 
		 //返回多指触摸的索引，可作为getPointerId(int pointerIndex)方法的参数

	public final int getAction()

	public final float getX()
	public final float getY()
		//当前的触摸点在视图坐标系上的坐标（相对于父View）

	public final float getRawX()
	public final float getRawY()
		//当前的触摸点在Android坐标系上的坐标（相对于屏幕）

## android.widget.Scroller ##

	public class Scroller

	public Scroller(Context context)

	public Scroller(Context context, Interpolator interpolator)

	public void startScroll(int startX, int startY, int dx, int dy, int duration)

	public void startScroll(int startX, int startY, int dx, int dy) {
        startScroll(startX, startY, dx, dy, DEFAULT_DURATION);
    }

	public boolean computeScrollOffset()  //返回true表示滑动中，false表示滑动完成

	public final int getCurrX()  //当前滑动到的坐标点的x坐标

	public final int getCurrY()  ////当前滑动到的坐标点的y坐标

	public void fling(int startX, int startY, int velocityX, int velocityY,
            int minX, int maxX, int minY, int maxY)

	public float getCurrVelocity()

## android.view.ViewConfiguration ##

	public static ViewConfiguration get(Context context)

	public int getScaledTouchSlop() {
        return mTouchSlop;
    }


## android.view.VelocityTracker ##

	static public VelocityTracker obtain()

	public void addMovement(MotionEvent event)

	public void computeCurrentVelocity(int units) 
		//参数表示计算速度所使用的单元时间，单位ms。即参数为1时，速度的单元时间就是1ms；
			参数为1000时，速度的单元时间就是1s

	public float getXVelocity()  
		//返回水平方向的速度，这里的速度就是指在单元时间内滑过的像素数。返回值为正，表示从左向右滑。
			返回值为负，表示从右向左滑。

	public float getYVelocity()

	public void clear()

	public void recycle()

## android.view.GestureDetector ##

	public GestureDetector(Context context, OnGestureListener listener)

	public GestureDetector(Context context, OnGestureListener listener, Handler handler)

	public boolean onTouchEvent(MotionEvent ev)

	public void setIsLongpressEnabled(boolean isLongpressEnabled)

### android.view.GestureDetector.OnGestureListener ###

	boolean onDown(MotionEvent e);
    
	void onShowPress(MotionEvent e);
	
	boolean onSingleTapUp(MotionEvent e);
	
	boolean onScroll(MotionEvent e1, MotionEvent e2, float distanceX, float distanceY);
	
	void onLongPress(MotionEvent e);
	
	boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY);

### android.view.GestureDetector.OnDoubleTapListener ###

	boolean onSingleTapConfirmed(MotionEvent e);

	boolean onDoubleTap(MotionEvent e);
	
	boolean onDoubleTapEvent(MotionEvent e);

### android.view.GestureDetector.OnContextClickListener ###

	boolean onContextClick(MotionEvent e);

### android.view.GestureDetector.SimpleOnGestureListener ###

	public static class SimpleOnGestureListener 
			implements OnGestureListener, OnDoubleTapListener, OnContextClickListener