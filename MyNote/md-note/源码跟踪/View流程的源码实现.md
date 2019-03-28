# 从Activity启动到View绘制的流程 #
## SystemServiceRegistry.java ##

	final class SystemServiceRegistry 

	static {
		...
		registerService(Context.WINDOW_SERVICE, WindowManager.class,
            new CachedServiceFetcher<WindowManager>() {
            @Override
            public WindowManager createService(ContextImpl ctx) {
                return new WindowManagerImpl(ctx);
            }});
	}
	
	

----
## WindowManagerImpl.java ##

	public final class WindowManagerImpl implements WindowManager 

	private final WindowManagerGlobal mGlobal = WindowManagerGlobal.getInstance();

	public WindowManagerImpl(Context context) {
    	this(context, null);
	}

    private WindowManagerImpl(Context context, Window parentWindow) {
        mContext = context;
        mParentWindow = parentWindow;
    }

	@Override
    public void addView(@NonNull View view, @NonNull ViewGroup.LayoutParams params) {
        applyDefaultToken(params);
        mGlobal.addView(view, params, mContext.getDisplay(), mParentWindow);
    }
	

----
## WindowManagerGlobal.java ##

	public final class WindowManagerGlobal

	private final ArrayList<View> mViews = new ArrayList<View>();
    private final ArrayList<ViewRootImpl> mRoots = new ArrayList<ViewRootImpl>();

    private final ArrayList<WindowManager.LayoutParams> mParams =
            new ArrayList<WindowManager.LayoutParams>();

	public void addView(View view, ViewGroup.LayoutParams params,
            Display display, Window parentWindow) {
	
		final WindowManager.LayoutParams wparams = (WindowManager.LayoutParams) params;

		ViewRootImpl root;
		View panelParentView = null;
		...
		root = new ViewRootImpl(view.getContext(), display);

        view.setLayoutParams(wparams);

        mViews.add(view);
        mRoots.add(root);
        mParams.add(wparams);

		mViews.add(view);
        mRoots.add(root);
        mParams.add(wparams);

		try {
            root.setView(view, wparams, panelParentView);
        } catch (RuntimeException e) {...}
		
	}

----
## ViewRootImpl.java ##

	public final class ViewRootImpl implements ViewParent,
        View.AttachInfo.Callbacks, ThreadedRenderer.DrawCallbacks

	public ViewRootImpl(Context context, Display display) {
		...
		mThread = Thread.currentThread();
		mWidth = -1;
        mHeight = -1;
        mDirty = new Rect();
        mTempRect = new Rect();
        mVisRect = new Rect();
        mWinFrame = new Rect();
        mWindow = new W(this);
		mFirst = true; // true for the first time the view is added
	
		mAttachInfo = new View.AttachInfo(mWindowSession, mWindow, display, 
				this, mHandler, this, context);
		...
	}
	
	//创建Activity的界面视图时，此view是DecorView；
	//通过WindowManager.addView直接将View控件添加到Window窗体时，视添加的View控件类型而定。当为DecorView时，不能使用硬件加速
	public void setView(View view, WindowManager.LayoutParams attrs, View panelParentView) {
        synchronized (this) {
			if (mView == null) {
                mView = view;
				
				mWindowAttributes.copyFrom(attrs);
				...
				//DecorView实现了RootViewSurfaceTaker接口
				if (view instanceof RootViewSurfaceTaker) {
                    mSurfaceHolderCallback =
                            ((RootViewSurfaceTaker)view).willYouTakeTheSurface();
                    if (mSurfaceHolderCallback != null) {
                        mSurfaceHolder = new TakenSurfaceHolder();
                        mSurfaceHolder.setFormat(PixelFormat.UNKNOWN);
                        mSurfaceHolder.addCallback(mSurfaceHolderCallback);
                    }
                }
				...
				// If the application owns the surface, don't enable hardware acceleration
                if (mSurfaceHolder == null) {
                    enableHardwareAcceleration(attrs);
                }
				...
				requestLayout();
				...
				view.assignParent(this);
				...
			}
		}
	}

	@Override
    public void requestLayout() {
        if (!mHandlingLayoutInLayoutRequest) {
            checkThread();
            mLayoutRequested = true;
            scheduleTraversals();
        }
    }

	void checkThread() {
        if (mThread != Thread.currentThread()) {
            throw new CalledFromWrongThreadException(
                    "Only the original thread that created a view hierarchy can touch its
					views.");
        }
    }

	void scheduleTraversals() {
        if (!mTraversalScheduled) {
            mTraversalScheduled = true;
            mTraversalBarrier = mHandler.getLooper().getQueue().postSyncBarrier();

            mChoreographer.postCallback(
                    Choreographer.CALLBACK_TRAVERSAL, mTraversalRunnable, null);
            ...
        }
    }

	final TraversalRunnable mTraversalRunnable = new TraversalRunnable();

	final class TraversalRunnable implements Runnable {
        @Override
        public void run() {
            doTraversal();
        }
    }

	void doTraversal() {
        if (mTraversalScheduled) {
            mTraversalScheduled = false;
            ...
            performTraversals();
			...
        }
    }

	private void performTraversals() {
		final View host = mView;
		...
		mIsInTraversal = true;
		mWillDrawSoon = true;
		WindowManager.LayoutParams lp = mWindowAttributes;
		...
		if (mFirst) {
			host.dispatchAttachedToWindow(mAttachInfo, 0);
		}

		boolean layoutRequested = mLayoutRequested && (!mStopped || mReportNextDraw);
        if (layoutRequested) {
			...
			// Ask host how big it wants to be
			windowSizeMayChange |= measureHierarchy(host, lp, res,
                    desiredWindowWidth, desiredWindowHeight);
		}
		...
		if (mApplyInsetsRequested) {
            ...
            dispatchApplyInsets(host);
            if (mLayoutRequested) {
                windowSizeMayChange |= measureHierarchy(host, lp,
                        mView.getContext().getResources(),
                        desiredWindowWidth, desiredWindowHeight);
            }
        }
		...
		if (mFirst || windowShouldResize || insetsChanged ||
                viewVisibilityChanged || params != null || mForceNextWindowRelayout) {
			...
			if (mSurfaceHolder != null) {
                ...
                for (SurfaceHolder.Callback c : callbacks) {
                    c.surfaceCreated(mSurfaceHolder);
                }
				...
                for (SurfaceHolder.Callback c : callbacks) {
                    c.surfaceChanged(mSurfaceHolder, lp.format,
                            mWidth, mHeight);
                }
				...
                for (SurfaceHolder.Callback c : callbacks) {
                    c.surfaceDestroyed(mSurfaceHolder);
                }
				...
            }
			...
			if (!mStopped || mReportNextDraw) {
                boolean focusChangedDueToTouchMode = ensureTouchModeLocally(
                        (relayoutResult&WindowManagerGlobal.RELAYOUT_RES_IN_TOUCH_MODE) != 0);
                if (focusChangedDueToTouchMode || mWidth != host.getMeasuredWidth()
                        || mHeight != host.getMeasuredHeight() || contentInsetsChanged ||
                        updatedConfiguration) {
                    int childWidthMeasureSpec = getRootMeasureSpec(mWidth, lp.width);
                    int childHeightMeasureSpec = getRootMeasureSpec(mHeight, lp.height);

                    if (DEBUG_LAYOUT) Log.v(mTag, "Ooops, something changed!  mWidth="
											+ mWidth + " measuredWidth=" + host.getMeasuredWidth()
											+ " mHeight=" + mHeight
											+ " measuredHeight=" + host.getMeasuredHeight()
											+ " coveredInsetsChanged=" + contentInsetsChanged);
                            
                    performMeasure(childWidthMeasureSpec, childHeightMeasureSpec);

                    int width = host.getMeasuredWidth();
                    int height = host.getMeasuredHeight();
                    boolean measureAgain = false;
                    ...
                    if (measureAgain) {

                        if (DEBUG_LAYOUT) Log.v(mTag,
                                "And hey let's measure once more: width=" + width
									+ " height=" + height);
									+ 
                        performMeasure(childWidthMeasureSpec, childHeightMeasureSpec);
                    }
                    layoutRequested = true;
                }
            }
			...
		}
		...
		final boolean didLayout = layoutRequested && (!mStopped || mReportNextDraw);
        ...
        if (didLayout) {
			performLayout(lp, mWidth, mHeight);
			...
		}
		...
		mFirst = false;
		mWillDrawSoon = false;
		...
		boolean cancelDraw = mAttachInfo.mTreeObserver.dispatchOnPreDraw() || !isViewVisible;
        if (!cancelDraw && !newSurface) {
			...
            performDraw();
        }
		...
		mIsInTraversal = false;
	}
	

	private boolean measureHierarchy(final View host, final WindowManager.LayoutParams lp,
            final Resources res, final int desiredWindowWidth, final int desiredWindowHeight) {
		...
		performMeasure(childWidthMeasureSpec, childHeightMeasureSpec);
		...
	}

	private void performMeasure(int childWidthMeasureSpec, int childHeightMeasureSpec) {
        ...
        mView.measure(childWidthMeasureSpec, childHeightMeasureSpec);
        ...
    }

	private void performLayout(WindowManager.LayoutParams lp, int desiredWindowWidth,
            int desiredWindowHeight) {
        mLayoutRequested = false;
        mScrollMayChange = true;
        mInLayout = true;

        final View host = mView;
        ...
        try {
            host.layout(0, 0, host.getMeasuredWidth(), host.getMeasuredHeight());

            mInLayout = false;

            int numViewsRequestingLayout = mLayoutRequesters.size();
            if (numViewsRequestingLayout > 0) {

                // requestLayout() was called during layout.
                // If no layout-request flags are set on the requesting views, there is no problem.
                // If some requests are still pending, then we need to clear those flags and do
                // a full request/measure/layout pass to handle this situation.

                ArrayList<View> validLayoutRequesters = getValidLayoutRequesters(mLayoutRequesters, false);
                if (validLayoutRequesters != null) {

                    // Set this flag to indicate that any further requests are happening during
                    // the second pass, which may result in posting those requests to the next
                    // frame instead

                    mHandlingLayoutInLayoutRequest = true;

                    // Process fresh layout requests, then measure and layout
                    int numValidRequests = validLayoutRequesters.size();
                    for (int i = 0; i < numValidRequests; ++i) {
                        final View view = validLayoutRequesters.get(i);

                        Log.w("View", "requestLayout() improperly called by " + view +
                                " during layout: running second layout pass");

                        view.requestLayout();
                    }


                    measureHierarchy(host, lp, mView.getContext().getResources(),
                            desiredWindowWidth, desiredWindowHeight);

                    mInLayout = true;

                    host.layout(0, 0, host.getMeasuredWidth(), host.getMeasuredHeight());

                    mHandlingLayoutInLayoutRequest = false;

                    // Check the valid requests again, this time without checking/clearing the
                    // layout flags, since requests happening during the second pass get noop'd

                    validLayoutRequesters = getValidLayoutRequesters(mLayoutRequesters, true);

                    if (validLayoutRequesters != null) {

                        final ArrayList<View> finalRequesters = validLayoutRequesters;

                        // Post second-pass requests to the next frame

                        getRunQueue().post(new Runnable() {
                            @Override
                            public void run() {
                                int numValidRequests = finalRequesters.size();
                                for (int i = 0; i < numValidRequests; ++i) {
                                    final View view = finalRequesters.get(i);

                                    Log.w("View", "requestLayout() improperly called by " + view +
                                            " during second layout pass: posting in next frame");

                                    view.requestLayout();
                                }
                            }
                        });
                    }
                }

            }
        } finally {
            Trace.traceEnd(Trace.TRACE_TAG_VIEW);
        }
        mInLayout = false;
    }

	private void performDraw() {
        ...
		//通常在performTraversals方法中执行此performDraw方法之前，mFullRedrawNeeded为设置成true
        final boolean fullRedrawNeeded = mFullRedrawNeeded;
        mFullRedrawNeeded = false;

        mIsDrawing = true;
        try {
            draw(fullRedrawNeeded);
        } finally {
            mIsDrawing = false;
            Trace.traceEnd(Trace.TRACE_TAG_VIEW);
        }
        ...
    }

	private void draw(boolean fullRedrawNeeded) {
		Surface surface = mSurface;
		...
		final float appScale = mAttachInfo.mApplicationScale;
		final Rect dirty = mDirty;
		...
		//fullRedrawNeeded通常为true
		if (fullRedrawNeeded) {
            mAttachInfo.mIgnoreDirtyState = true;
            dirty.set(0, 0, (int) (mWidth * appScale + 0.5f), (int) (mHeight * appScale + 0.5f));
        }
        ...
        mAttachInfo.mTreeObserver.dispatchOnDraw();
		...
        if (!dirty.isEmpty() || mIsAnimating || accessibilityFocusDirty) {
            if (mAttachInfo.mThreadedRenderer != null && mAttachInfo.mThreadedRenderer.isEnabled()) {
				...
                mAttachInfo.mThreadedRenderer.draw(mView, mAttachInfo, this);
            } else {
				...
				//因为Activity的根View是DecorView，所以执行drawSoftware方法
                if (!drawSoftware(surface, mAttachInfo, xOffset, yOffset, scalingRequired, dirty)) {
                    return;
                }
            }
        }
		...
    }

	//return true if drawing was successful, false if an error occurred
	private boolean drawSoftware(Surface surface, AttachInfo attachInfo, int xoff, int yoff,
            boolean scalingRequired, Rect dirty) {

        final Canvas canvas;
        try {
            ...
            canvas = mSurface.lockCanvas(dirty);
            ...
        } catch (Surface.OutOfResourcesException e) {
            handleOutOfResourcesException(e);
            return false;
        } catch (IllegalArgumentException e) {
            Log.e(mTag, "Could not lock surface", e);
            mLayoutRequested = true;    // ask wm for a new surface next time.
            return false;
        }

        try {
            ...
                mView.draw(canvas);
			...
        } finally {
            try {
                surface.unlockCanvasAndPost(canvas);
            } catch (IllegalArgumentException e) {
                Log.e(mTag, "Could not unlock surface", e);
                mLayoutRequested = true;    // ask wm for a new surface next time.
                return false;
            }
        }
        return true;
    }

	//如果此方法没有执行，mAttachInfo.mThreadedRenderer == null
	private void enableHardwareAcceleration(WindowManager.LayoutParams attrs) {
        mAttachInfo.mHardwareAccelerated = false;
        mAttachInfo.mHardwareAccelerationRequested = false;
        ...
        // Try to enable hardware acceleration if requested
        final boolean hardwareAccelerated =
                (attrs.flags & WindowManager.LayoutParams.FLAG_HARDWARE_ACCELERATED) != 0;

        if (hardwareAccelerated) {
            if (!ThreadedRenderer.isAvailable()) {
                return;
            }
            ...
                mAttachInfo.mThreadedRenderer = ThreadedRenderer.create(mContext, translucent,
                        attrs.getTitle().toString());
                if (mAttachInfo.mThreadedRenderer != null) {
                    mAttachInfo.mHardwareAccelerated =
                            mAttachInfo.mHardwareAccelerationRequested = true;
                }
            ...
        }
    }

	********************************************************************

	boolean requestLayoutDuringLayout(final View view) {
        if (view.mParent == null || view.mAttachInfo == null) {
            // Would not normally trigger another layout, so just let it pass through as usual
            return true;
        }
        if (!mLayoutRequesters.contains(view)) {
            mLayoutRequesters.add(view);
        }
        if (!mHandlingLayoutInLayoutRequest) {
            // Let the request proceed normally; it will be processed in a second layout pass
            // if necessary
            return true;
        } else {
            // Don't let the request proceed during the second layout pass.
            // It will post to the next frame instead.
            return false;
        }
    }

	******************************************************************

	@Override
    public ViewParent invalidateChildInParent(int[] location, Rect dirty) {
        checkThread();
        if (DEBUG_DRAW) Log.v(mTag, "Invalidate child: " + dirty);

		//dirty通常不为null
        if (dirty == null) {
            invalidate();
            return null;
        } else if (dirty.isEmpty() && !mIsAnimating) {
            return null;
        }
        ...
        invalidateRectOnScreen(dirty);

        return null;
    }

	private void invalidateRectOnScreen(Rect dirty) {
        final Rect localDirty = mDirty;
        ...
        final float appScale = mAttachInfo.mApplicationScale;
        final boolean intersected = localDirty.intersect(0, 0,
                (int) (mWidth * appScale + 0.5f), (int) (mHeight * appScale + 0.5f));
        if (!intersected) {
            localDirty.setEmpty();
        }
        if (!mWillDrawSoon && (intersected || mIsAnimating)) {
            scheduleTraversals();
        }
    }

	**************************************************************

	public void dispatchInvalidateDelayed(View view, long delayMilliseconds) {
        Message msg = mHandler.obtainMessage(MSG_INVALIDATE, view);
        mHandler.sendMessageDelayed(msg, delayMilliseconds);
    }

	final class ViewRootHandler extends Handler {
		...
		@Override
        public void handleMessage(Message msg) {
            switch (msg.what) {
	            case MSG_INVALIDATE:
	                ((View) msg.obj).invalidate();
	                break;
				...
			}
		}
	}

	final ViewRootHandler mHandler = new ViewRootHandler();
	

----
## ActivityThread.java ##

	final void handleResumeActivity(IBinder token,
            boolean clearHide, boolean isForward, boolean reallyResume, int seq, String reason) {

		ActivityClientRecord r = mActivities.get(token);

		r = performResumeActivity(token, clearHide, reason);

		if (r != null) {
			final Activity a = r.activity;

			r.window = r.activity.getWindow();
            View decor = r.window.getDecorView();
            decor.setVisibility(View.INVISIBLE);
            ViewManager wm = a.getWindowManager();
            WindowManager.LayoutParams l = r.window.getAttributes();
            a.mDecor = decor;
			...
				if (!a.mWindowAdded) {
	                a.mWindowAdded = true;
	                wm.addView(decor, l);
	            }
		}
	}


----
## Activity.java ##

	final void attach(Context context, ActivityThread aThread,
            Instrumentation instr, IBinder token, int ident,
            Application application, Intent intent, ActivityInfo info,
            CharSequence title, Activity parent, String id,
            NonConfigurationInstances lastNonConfigurationInstances,
            Configuration config, String referrer, IVoiceInteractor voiceInteractor,
            Window window, ActivityConfigCallback activityConfigCallback) {

		mWindow = new PhoneWindow(this, window, activityConfigCallback);
        mWindow.setWindowControllerCallback(this);
        mWindow.setCallback(this);

		mUiThread = Thread.currentThread();

        mMainThread = aThread;
	
		mWindow.setWindowManager(
                (WindowManager)context.getSystemService(Context.WINDOW_SERVICE),
                mToken, mComponent.flattenToString(),
                (info.flags & ActivityInfo.FLAG_HARDWARE_ACCELERATED) != 0);

        if (mParent != null) {
            mWindow.setContainer(mParent.getWindow());
        }
        mWindowManager = mWindow.getWindowManager();

	}

	public WindowManager getWindowManager() {
        return mWindowManager;
    }

----
## Window.java ##

	public void setWindowManager(WindowManager wm, IBinder appToken, String appName,
            boolean hardwareAccelerated) {
        mAppToken = appToken;
        mAppName = appName;
        mHardwareAccelerated = hardwareAccelerated
                || SystemProperties.getBoolean(PROPERTY_HARDWARE_UI, false);
        if (wm == null) {
            wm = (WindowManager)mContext.getSystemService(Context.WINDOW_SERVICE);
        }
        mWindowManager = ((WindowManagerImpl)wm).createLocalWindowManager(this);
    }

	public WindowManager getWindowManager() {
        return mWindowManager;
    }

----
## PhoneWindow.java ##

	public class PhoneWindow extends Window implements MenuBuilder.Callback

----
## DecorView.java ##

	public class DecorView extends FrameLayout implements RootViewSurfaceTaker, WindowCallbacks

----
## View.java ##

	public void requestLayout() {
        if (mMeasureCache != null) mMeasureCache.clear();

        if (mAttachInfo != null && mAttachInfo.mViewRequestingLayout == null) {
            // Only trigger request-during-layout logic if this is the view requesting it,
            // not the views in its parent hierarchy
            ViewRootImpl viewRoot = getViewRootImpl();
            if (viewRoot != null && viewRoot.isInLayout()) {
                if (!viewRoot.requestLayoutDuringLayout(this)) {
                    return;
                }
            }
            mAttachInfo.mViewRequestingLayout = this;
        }

        mPrivateFlags |= PFLAG_FORCE_LAYOUT;
        mPrivateFlags |= PFLAG_INVALIDATED;

        if (mParent != null && !mParent.isLayoutRequested()) {
            mParent.requestLayout();
        }
        if (mAttachInfo != null && mAttachInfo.mViewRequestingLayout == this) {
            mAttachInfo.mViewRequestingLayout = null;
        }
    }

	public ViewRootImpl getViewRootImpl() {
        if (mAttachInfo != null) {
            return mAttachInfo.mViewRootImpl;
        }
        return null;
    }

	void dispatchAttachedToWindow(AttachInfo info, int visibility) {
		mAttachInfo = info;
		...
	}

	void assignParent(ViewParent parent) {
        if (mParent == null) {
            mParent = parent;
        } 
		...
    }

	*******************************************************

	public void invalidate() {
        invalidate(true);
    }

	public void invalidate(boolean invalidateCache) {
        invalidateInternal(0, 0, mRight - mLeft, mBottom - mTop, invalidateCache, true);
    }

	void invalidateInternal(int l, int t, int r, int b, boolean invalidateCache,
            boolean fullInvalidate) {
        ...
            // Propagate the damage rectangle to the parent view.
            final AttachInfo ai = mAttachInfo;
            final ViewParent p = mParent;
            if (p != null && ai != null && l < r && t < b) {
                final Rect damage = ai.mTmpInvalRect;
                damage.set(l, t, r, b);
                p.invalidateChild(this, damage);
            }
        ...
    }

	*******************************************************	

	public void postInvalidate() {
        postInvalidateDelayed(0);
    }

	public void postInvalidateDelayed(long delayMilliseconds) {
        final AttachInfo attachInfo = mAttachInfo;
        if (attachInfo != null) {
            attachInfo.mViewRootImpl.dispatchInvalidateDelayed(this, delayMilliseconds);
        }
    }

	*********************************************

	public void setLayoutParams(ViewGroup.LayoutParams params) {
        if (params == null) {
            throw new NullPointerException("Layout parameters cannot be null");
        }
        mLayoutParams = params;
        resolveLayoutParams();
        if (mParent instanceof ViewGroup) {
            ((ViewGroup) mParent).onSetLayoutParams(this, params);
        }
        requestLayout();
    }

----
## ViewGroup.java ##
	
	public abstract class ViewGroup extends View implements ViewParent, ViewManager

	@Override
    void dispatchAttachedToWindow(AttachInfo info, int visibility) {
		...
        super.dispatchAttachedToWindow(info, visibility);
		...
        final int count = mChildrenCount;
        final View[] children = mChildren;

        for (int i = 0; i < count; i++) {
            final View child = children[i];
            child.dispatchAttachedToWindow(info,
                    combineVisibility(visibility, child.getVisibility()));
        }
        ...
    }

	private void addViewInner(View child, int index, LayoutParams params,
            boolean preventRequestLayout) {
        ...
        if (child.getParent() != null) {
            throw new IllegalStateException("The specified child already has a parent. " +
                    "You must call removeView() on the child's parent first.");
        }
		...
        if (preventRequestLayout) {
            child.mLayoutParams = params;
        } else {
            child.setLayoutParams(params); //setLayoutParams方法内部会调到requestLayout();
        }

        if (index < 0) {
            index = mChildrenCount;
        }

        addInArray(child, index);

        // tell our children

        if (preventRequestLayout) {
            child.assignParent(this);
        } else {
            child.mParent = this;
        }
        ...
        AttachInfo ai = mAttachInfo;
        if (ai != null && (mGroupFlags & FLAG_PREVENT_DISPATCH_ATTACHED_TO_WINDOW) == 0) {
            ...
            child.dispatchAttachedToWindow(mAttachInfo, (mViewFlags&VISIBILITY_MASK));
            ...
        }
		...
        dispatchViewAdded(child);
        ...
    }

	//ViewGroup的其他重载的addView方法内部都会调用到此addView
	public void addView(View child, int index, LayoutParams params) {
		...
        // addViewInner() will call child.requestLayout() when setting the new LayoutParams
        // therefore, we call requestLayout() on ourselves before, so that the child's request
        // will be blocked at our level

        requestLayout();
        invalidate(true);

        addViewInner(child, index, params, false);
    }

	*********************************************

	@Override
    public final void invalidateChild(View child, final Rect dirty) {
        final AttachInfo attachInfo = mAttachInfo;
        ...

        ViewParent parent = this;
        if (attachInfo != null) {
            ...
			final int[] location = attachInfo.mInvalidateChildLocation;
            location[CHILD_LEFT_INDEX] = child.mLeft;
            location[CHILD_TOP_INDEX] = child.mTop;
			...
            do {
                ...
				//一直向上得到最终父控件，即ViewRootImpl对象，
				//因为ViewRootImpl对象不为null，执行ViewRootImpl的invalidateChildInParent方法
                parent = parent.invalidateChildInParent(location, dirty);
                ...
            } while (parent != null);
        }
    }

	@Override
    public ViewParent invalidateChildInParent(final int[] location, final Rect dirty) {
        if ((mPrivateFlags & (PFLAG_DRAWN | PFLAG_DRAWING_CACHE_VALID)) != 0) {
            // either DRAWN, or DRAWING_CACHE_VALID
            ...
            return mParent; //返回当前控件的父控件
        }
        return null;
    }

# invalid的流程 #
# postInvalid的流程 #
# requestLayout()流程 #

# View的测量流程 #
**ViewRootImpl.java**

	private void performMeasure(int childWidthMeasureSpec, int childHeightMeasureSpec) {
        if (mView == null) {
            return;
        }
        Trace.traceBegin(Trace.TRACE_TAG_VIEW, "measure");
        try {
            mView.measure(childWidthMeasureSpec, childHeightMeasureSpec);
        } finally {
            Trace.traceEnd(Trace.TRACE_TAG_VIEW);
        }
    }

	private boolean measureHierarchy(final View host, final WindowManager.LayoutParams lp,
            final Resources res, final int desiredWindowWidth, final int desiredWindowHeight) {
        int childWidthMeasureSpec;
        int childHeightMeasureSpec;
		...
		if (!goodMeasure) {
            childWidthMeasureSpec = getRootMeasureSpec(desiredWindowWidth, lp.width);
            childHeightMeasureSpec = getRootMeasureSpec(desiredWindowHeight, lp.height);
            performMeasure(childWidthMeasureSpec, childHeightMeasureSpec);
            ...
        }
		...
	}

	//windowSize表示容器（这里特指window）可用的尺寸大小；rootDimension表示子元素在容器中布局的尺寸
		参数（layoutparams.width|layoutparams.height）。
		这个尺寸参数可以是一个特定的尺寸大小，于是子元素在容器中按照此大小精确测量；
		尺寸参数也可以是match_parent，即子元素的尺寸大小等于容器中可用的尺寸大小，即大小为
		windowSize；
		尺寸参数也可以为wrap_parent，即子元素的尺寸大小还不能确定，但不会超过容器中可用的尺寸大小
		windowsize。
	//返回值表示在容器中对子元素进行测量时，所使用的测量规格childMeasureSpec，包括子元素的测量模式，
		和跟此模式有关的尺寸大小。
	//将返回的对子元素在容器中进行测量的规格childMeasureSpec传递给子元素的measure(int, int)方法，
		开始对子元素进行测量.

	private static int getRootMeasureSpec(int windowSize, int rootDimension) {
        int measureSpec;
        switch (rootDimension) {

        case ViewGroup.LayoutParams.MATCH_PARENT:
            // Window can't resize. Force root view to be windowSize.
            measureSpec = MeasureSpec.makeMeasureSpec(windowSize, MeasureSpec.EXACTLY);
            break;
        case ViewGroup.LayoutParams.WRAP_CONTENT:
            // Window can resize. Set max size for root view.
            measureSpec = MeasureSpec.makeMeasureSpec(windowSize, MeasureSpec.AT_MOST);
            break;
        default:
            // Window wants to be an exact size. Force root view to be that size.
            measureSpec = MeasureSpec.makeMeasureSpec(rootDimension, MeasureSpec.EXACTLY);
            break;
        }
        return measureSpec;
    }

**View.java**

	//因为measure方法被final修饰，所以不能被重写了。于是对于测量方式不同的View控件，只有通过重写
		onMeasure方法来制定适合控件自身的测量方式。
	//widthMeasureSpec|heightMeasureSpec中的测量模式，是此View控件对自身进行测量的模式。如果
		此View控件是一个容器，那么必须要重写onMeasure方法，在onMeasure方法中，再另外为子元素制定
		测量规格。
	//子元素的测量规格通常是在容器中制定的，规格中测量的尺寸大小跟测量模式有关，如果测量模式是
		AT_MOST，那么测量子元素的尺寸大小不会超过容器中可用的尺寸大小。如果测量模式是EXACTLY，那么
		测量子元素的大小要么就是容器的大小（match_parent），要么就是一个精确值。如果测量模式是
		UNSPECIFIED，那么表示在容器中并没有为子元素制定好测量的尺寸大小，此时，测量子元素的尺寸大小
		至少要等于子元素自己设置的最小尺寸大小（可以为0）。
	//所以，参数widthMeasureSpec测量规格中的SpecMode测量模式如果是AT_MOST，那么SpecSize测量尺寸
		大小就是容器的可用尺寸大小。
		如果SpecMode是EXACTLY，那么SpecSize测量尺寸大小可能是容器的可用尺寸大小，也可能一个特定的
		尺寸大小。
		如果SpecMode是UNSPECIFIED，那么SpecSize是未定义的，一般不会被使用。
		
	public final void measure(int widthMeasureSpec, int heightMeasureSpec) {
		...
		onMeasure(widthMeasureSpec, heightMeasureSpec);
		...
	}

	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        setMeasuredDimension(getDefaultSize(getSuggestedMinimumWidth(), widthMeasureSpec),
                getDefaultSize(getSuggestedMinimumHeight(), heightMeasureSpec));
    }

	//如果测量此View控件的规格中的测量模式是UNSPECIFIED，即测量规格中没有制定测量尺寸，那么采用参数
		size作为默认的测量尺寸大小；如果测量规格中的测量模式为AT_MOST或EXACTLY，则说明测量规格中
		制定了测量尺寸，并且此时默认就使用测量规格中的测量尺寸大小。
	public static int getDefaultSize(int size, int measureSpec) {
        int result = size;
        int specMode = MeasureSpec.getMode(measureSpec);
        int specSize = MeasureSpec.getSize(measureSpec);

        switch (specMode) {
        case MeasureSpec.UNSPECIFIED:
            result = size;
            break;
        case MeasureSpec.AT_MOST:
        case MeasureSpec.EXACTLY:
            result = specSize;
            break;
        }
        return result;
    }

	//如果没有为此View控件设置背景Drawable图片，那么测量此View控件的最小测量尺寸就根据属性mMinWidth
		（mMinHeight）确定，如果设置了背景Drawable图片，那么最小测量尺寸就根据Drawable图片的固有
		大小确定。
	protected int getSuggestedMinimumWidth() {
        return (mBackground == null) ? mMinWidth : 
				max(mMinWidth, mBackground.getMinimumWidth());
    }

	protected int getSuggestedMinimumHeight() {
        return (mBackground == null) ? mMinHeight : 
				max(mMinHeight, mBackground.getMinimumHeight());
    }

	protected final void setMeasuredDimension(int measuredWidth, int measuredHeight) {
        ...
        setMeasuredDimensionRaw(measuredWidth, measuredHeight);
    }

	//为此View控件设置测量时的测量尺寸大小（宽高）
	private void setMeasuredDimensionRaw(int measuredWidth, int measuredHeight) {
        mMeasuredWidth = measuredWidth;
        mMeasuredHeight = measuredHeight;

        mPrivateFlags |= PFLAG_MEASURED_DIMENSION_SET;
    }

	//此方法在一个容器控件中调用时，参数size表示此容器所需要的尺寸大小（受子元素的测量尺寸大小影响），
		参数measureSpec表示在父容器中根据此子元素的布局参数layoutparams中的尺寸参数(layoutparams
		.width|layoutparams.height)，以及父容器的可用尺寸大小，制定的对子元素的测量规格。如果
		measureSpec中的SpecMode是AT_MOST，那么说明measureSpec中的SpecSize就是父容器的可用尺寸
		大小，并且对于AT_MOST测量模式，子元素在测量时的尺寸大小是不能超过SpecSize的大小的。所以，在
		AT_MOST模式下，当子元素所需要的尺寸大小size超过了父容器可用的尺寸大小SpecSize时，还是要以
		SpecSize作为测量尺寸对这个子元素容器进行测量。此时，会对子元素容器中的控件产生影响，即不会
		完成显示出子元素容器中的控件，因为父容器可以给子容器使用的尺寸大小不够。
	//当SpecMode为EXACTLY时，测量使用的尺寸大小SpecSize是有可能超过父容器的可用尺寸大小的。
	public static int resolveSizeAndState(int size, int measureSpec, 
			int childMeasuredState) {

        final int specMode = MeasureSpec.getMode(measureSpec);
        final int specSize = MeasureSpec.getSize(measureSpec);
        final int result;
        switch (specMode) {
            case MeasureSpec.AT_MOST:
				
                if (specSize < size) {
                    result = specSize | MEASURED_STATE_TOO_SMALL;
                } else {
                    result = size;
                }
                break;
            case MeasureSpec.EXACTLY:
                result = specSize;
                break;
            case MeasureSpec.UNSPECIFIED:
            default:
                result = size;
        }
        return result | (childMeasuredState & MEASURED_STATE_MASK);
    }


**ViewGroup.java**

	protected void measureChildWithMargins(View child,
            int parentWidthMeasureSpec, int widthUsed,
            int parentHeightMeasureSpec, int heightUsed) {
        final MarginLayoutParams lp = (MarginLayoutParams) child.getLayoutParams();

        final int childWidthMeasureSpec = getChildMeasureSpec(parentWidthMeasureSpec,
                mPaddingLeft + mPaddingRight + lp.leftMargin + lp.rightMargin
                    	+ widthUsed, lp.width);
                    	
        final int childHeightMeasureSpec = getChildMeasureSpec(parentHeightMeasureSpec,
                mPaddingTop + mPaddingBottom + lp.topMargin + lp.bottomMargin
                   		+ heightUsed, lp.height);

        child.measure(childWidthMeasureSpec, childHeightMeasureSpec);
    }

	//此方法在一个容器控件中调用，用于制定子元素的测量规格。参数spec表示容器控件的测量规格，
		参数padding表示容器控件中不能用于测量子元素的尺寸大小（包括容器的内边距尺寸，
		以及子元素和子元素之间的间隔尺寸），在制定子元素的测量尺寸大小resultSize时，
		应该在容器控件测量规格spec中规定的容器的测量尺寸specSize的尺寸范围内，减去容器中不可用于
		测量子元素的尺寸大小padding，即在容器控件中，
		子元素的可用测量尺寸大小size为：容器的specSize - 容器中不可用的padding。
		参数childDimension表示容器中子元素的布局参数中的尺寸参数（layoutparams.widht|height），

		一. 如果子元素的布局尺寸参数childDimension是一个确定值，那么子元素的测量尺寸大小
		resultSize就等于childDimension，并且子元素的测量模式specMode为EXACTLY；

		二. 如果子元素的布局尺寸参数childDimension不是确定值，那么需要考虑到容器的测量模式，
		和子元素在容器中可用于测量的尺寸大小。
		如： 
		a. 当子元素的布局尺寸参数childDimensionSion是match_parent时，那么子元素的
		测量尺寸大小resultSize就等于容器给予子元素的可用尺寸大小size，此时，① 如果容器的测量模式
		specMode是EXACLTY，那么子元素的测量模式resultMode也是EXACLTY，
		② 如果容器的测量模式是AT_MOST时，说明在制定子元素的测量规格时，容器的测量尺寸并没有被确定好，
		只知道容器的测量尺寸不会超过specSize。因为容器的测量尺寸还没有确定，所以子元素的测量尺寸
		也还不能确定，姑且让子元素的测量尺寸不超过容器给予的可用尺寸size，即设置子元素的测量模式
		resultMode为AT_MOST，resultSize为size。
		③ 如果容器的测量模式是UNSPECIFIED，说明容器的测量尺寸是未指定的，而子元素的布局尺寸参数又是
		match_parent，所以子元素的测量模式resultMode也是UNSPECIFIED未指定的，并且子元素的测量
		尺寸resultSize通常会被设置为0。(UNSPECIFIED这种测量模式，通常只有在系统内部会用到)
		b. 当子元素的布局尺寸参数childDimension是wrap_content时，表示子元素的测量尺寸resultSize
		由子元素的内容尺寸确定，由于在容器中制定子元素的测量规格时，还没有开始进入子元素测量过程，所以
		此时子元素的内容尺寸是没有确定好的，① 但当容器的测量模式是AT_MOST或EXCATLY时，我们至少可以
		确定子元素在容器中的可用测量尺寸为size，即子元素的内容尺寸不能超过可用测量尺寸。于是可以先将
		子元素的测量尺寸resultSize设置为size，测量模式resultMode设置为AT_MOST。这样，当之后进入
		子元素的测量过程时，我们在根据子元素的具体情况调用setMeasuredDimension方法为子元素设置测量
		尺寸大小的实际值。②不过当容器的测量模式是UNSPECIFIED时，说明容器的测量尺寸是未指定的，又因为
		子元素的内容尺寸也还不能确定，所以此时子元素的测量模式resultMode也是UNSPECIFIED未指定的，
		并且当resultMode测量模式为UNSPECIFIED时，resultSize测量尺寸通常会被设置为0。
		

		对于一个非容器的View控件，在它的测量过程measure(int widthMeasureSepc, 
		int heightMeasureSpec)中，我们完全可以调用setMeasuredDimension方法确定好它的测量尺寸大小
		（mMeasuredWidth|mMeasuredHeight），① 对于SpecMode为EXACLTY的情况，测量尺寸大小就是
		SpecSize（或者特定View控件的onMeasure方法中设置的其他值），② 对于SpecMode为AT_MOST的情况，
		测量尺寸大小通常就是特定View控件设置的不超过SpecSize的值（有时这个值也会超过SpecSize）。
		③ 对于SpecMode为UNSPECIFIED的情况，测量尺寸大小有特定View控件自己决定。不用参考SpecSize。
		不管这个非容器View控件在它的测量过程中所设置的测量尺寸大小，有没有满足父容器为它制定的测量规格
		中的约束条件。至少在子元素的测量过程结束之后，我们已经可以通过子元素的getMeasuredWidth|
		getMeasuredHeight方法来获取子元素的测量尺寸大小了（尽管这可能不是最终的测量尺寸大小）。

		对于一个容器类的View控件，在它的测量过程measure(int widthMeasureSepc, 
		int heightMeasureSpec)中，① 如果SpecMode为EXACTLY，我们是可以调用setMeasuredDimension
		方法直接将此容器控件的测量尺寸大小（mMeasuredWidth|mMeasuredHeight）设置为SpecSize；
		但是，② 如果SpecMode为AT_MOST时，我们通常就需要根据此容器控件的内容来确定容器的测量尺寸
		大小了。而容器控件的内容还是一个View控件，设置这个内容控件还有可能是一个嵌套的容器控件。
		此时，只有在执行完所有内容控件的测量流程之后，才能根据内容控件提供的测量尺寸大小（内容控件的
		getMeasuredWidth|getMeasuredHeight方法），来计算容器控件的测量尺寸大小了。③ 对于SpecMode
		为UNSPECIFIED的情况，容器控件通常也是需要根据内容控件的测量尺寸大小来计算容器控件的测量尺寸
		大小。

	public static int getChildMeasureSpec(int spec, int padding, int childDimension) {
        int specMode = MeasureSpec.getMode(spec);
        int specSize = MeasureSpec.getSize(spec);

        int size = Math.max(0, specSize - padding);

        int resultSize = 0;
        int resultMode = 0;

        switch (specMode) {
        // Parent has imposed an exact size on us
        case MeasureSpec.EXACTLY:
            if (childDimension >= 0) {
                resultSize = childDimension;
                resultMode = MeasureSpec.EXACTLY;
            } else if (childDimension == LayoutParams.MATCH_PARENT) {
                // Child wants to be our size. So be it.
                resultSize = size;
                resultMode = MeasureSpec.EXACTLY;
            } else if (childDimension == LayoutParams.WRAP_CONTENT) {
                // Child wants to determine its own size. It can't be
                // bigger than us.
                resultSize = size;
                resultMode = MeasureSpec.AT_MOST;
            }
            break;

        // Parent has imposed a maximum size on us
        case MeasureSpec.AT_MOST:
            if (childDimension >= 0) {
                // Child wants a specific size... so be it
                resultSize = childDimension;
                resultMode = MeasureSpec.EXACTLY;
            } else if (childDimension == LayoutParams.MATCH_PARENT) {
                // Child wants to be our size, but our size is not fixed.
                // Constrain child to not be bigger than us.
                resultSize = size;
                resultMode = MeasureSpec.AT_MOST;
            } else if (childDimension == LayoutParams.WRAP_CONTENT) {
                // Child wants to determine its own size. It can't be
                // bigger than us.
                resultSize = size;
                resultMode = MeasureSpec.AT_MOST;
            }
            break;

        // Parent asked to see how big we want to be
        case MeasureSpec.UNSPECIFIED:
            if (childDimension >= 0) {
                // Child wants a specific size... let him have it
                resultSize = childDimension;
                resultMode = MeasureSpec.EXACTLY;
            } else if (childDimension == LayoutParams.MATCH_PARENT) {
                // Child wants to be our size... find out how big it should
                // be
                resultSize = View.sUseZeroUnspecifiedMeasureSpec ? 0 : size;
                resultMode = MeasureSpec.UNSPECIFIED;
            } else if (childDimension == LayoutParams.WRAP_CONTENT) {
                // Child wants to determine its own size.... find out how
                // big it should be
                resultSize = View.sUseZeroUnspecifiedMeasureSpec ? 0 : size;
                resultMode = MeasureSpec.UNSPECIFIED;
            }
            break;
        }
        //noinspection ResourceType
        return MeasureSpec.makeMeasureSpec(resultSize, resultMode);
    }

**FrameLayout.java （DecorView extends FrameLayout）**

	@Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        int count = getChildCount();

		//当测量容器控件时的measureSpec中的SpecMode是AT_MOST时，只能知道测量此容器控件时的尺寸大小
			不能超过父容器的可用尺寸大小SpecSize，并不能确定测量该容器控件所使用的尺寸大小的确定值。
			因为还没有确定该容器控件的测量尺寸大小，所以子元素的测量尺寸大小就不能确定。
			只有先知道所有子元素的测量模式之后才能确定该容器控件的尺寸大小。
			所以，需要先走一遍子元素的测量流程之后，才能确定该容器控件的尺寸大小；在知道了该容器控件
			的尺寸大小之后，再走一遍子元素的测量流程来确定子元素的测量尺寸大小。
		//如果测量该容器控件的SpecMode是EXACTLY，说明该容器的测量尺寸大小就是SpecSize，不需要再
			走第二遍子元素的测量流程了。
		//容器控件的测量尺寸大小受子元素的测量模式和测量尺寸的影响，而子元素的测量模式和测量尺寸受子元	素的布局参数中的尺寸参数(layoutparams.width|height)的影响，当布局参数中的尺寸参数是	match_parent或wrap_parent时，子元素的测量模式还会受到容器控件的测量模式的影响。

		final boolean measureMatchParentChildren =
                MeasureSpec.getMode(widthMeasureSpec) != MeasureSpec.EXACTLY ||
                MeasureSpec.getMode(heightMeasureSpec) != MeasureSpec.EXACTLY;
        mMatchParentChildren.clear();

		for (int i = 0; i < count; i++) {
            final View child = getChildAt(i);
            if (mMeasureAllChildren || child.getVisibility() != GONE) {

                measureChildWithMargins(child, widthMeasureSpec, 0, heightMeasureSpec, 0);

                final LayoutParams lp = (LayoutParams) child.getLayoutParams();

				//比较每个子元素测量后得到的测量尺寸，取最大的那个子元素的测量尺寸作为容器的测量尺寸
					的参考
                maxWidth = Math.max(maxWidth,
                        child.getMeasuredWidth() + lp.leftMargin + lp.rightMargin);
                maxHeight = Math.max(maxHeight,
                        child.getMeasuredHeight() + lp.topMargin + lp.bottomMargin);
                ...
                if (measureMatchParentChildren) {
                    if (lp.width == LayoutParams.MATCH_PARENT ||
                            lp.height == LayoutParams.MATCH_PARENT) {
                        mMatchParentChildren.add(child);
                    }
                }
            }
        }
		...
		setMeasuredDimension(resolveSizeAndState(maxWidth, widthMeasureSpec, childState),
                resolveSizeAndState(maxHeight, heightMeasureSpec,
                        childState << MEASURED_HEIGHT_STATE_SHIFT));

		//以下步骤可以解决这种情况：
			在之前为该容器的子元素制定测量规格的时候，如果容器的测量模式是AT_MOST，那么当子元素
			的布局尺寸参数(layoutparams.width|layoutparams.height)为match_parent时，考虑到
			在没有遍历完所有子元素的测量过程并为该容器确定测量尺寸之前，即在不知道该容器的测量尺寸的
			情况下，姑且把match_parent布局尺寸参数所对应的子元素的测量模式设置为AT_MOST，于是在
			确定子元素的测量尺寸的时候，在不超出SpecSize的情况下，是以子元素的内容尺寸为参考的。
			于是，在没有确定容器测量尺寸的情况下，因为各个子元素的测量尺寸是以各自的内容尺寸为参考的，
			而有的子元素的内容尺寸大，有的子元素的内容尺寸小，于是不同子元素有着不同的测量尺寸，可是
			因为此情况下，这些不同测量尺寸大小的子元素的布局尺寸参数是match_parent的，即这些子元素
			的测量尺寸本来要是一样大小的（都等于容器中对子元素可用的测量尺寸）。只不过在没有确定
			好容器的测量尺寸之前，无法满足match_parent的要求。
			但是，执行完容器的setMeasuredDimension方法后，容器的测量尺寸是确定好了的，于是对于
			容器的测量模式为AT_MOST，并且子元素的布局尺寸参数是match_parent的情况，我们需要以
			容器的测量尺寸大小(getMeasuredWidth|getMeasuredHeight)为参考，重新为目标子元素制定
			测量规格，并对目标子元素再进行一次测量过程。
        count = mMatchParentChildren.size();
        if (count > 1) {
            for (int i = 0; i < count; i++) {

                final View child = mMatchParentChildren.get(i);

                final MarginLayoutParams lp = (MarginLayoutParams) child.getLayoutParams();

                final int childWidthMeasureSpec;
                if (lp.width == LayoutParams.MATCH_PARENT) {

                    final int width = Math.max(0, 
							getMeasuredWidth() - getPaddingLeftWithForeground() 
							- getPaddingRightWithForeground()
                        	- lp.leftMargin - lp.rightMargin);
                        	- 
                    childWidthMeasureSpec = MeasureSpec.makeMeasureSpec(
                            width, MeasureSpec.EXACTLY);

                } else {
                    childWidthMeasureSpec = getChildMeasureSpec(widthMeasureSpec,
                            getPaddingLeftWithForeground() 
							+ getPaddingRightWithForeground() 
                        	+ lp.leftMargin + lp.rightMargin,
                            lp.width);
                }

                final int childHeightMeasureSpec;

                if (lp.height == LayoutParams.MATCH_PARENT) {

                    final int height = Math.max(0, getMeasuredHeight()
                        	- getPaddingTopWithForeground() 
                       		- getPaddingBottomWithForeground()
	                        - lp.topMargin - lp.bottomMargin);
	                        
                    childHeightMeasureSpec = MeasureSpec.makeMeasureSpec(
                            height, MeasureSpec.EXACTLY);

                } else {

                    childHeightMeasureSpec = getChildMeasureSpec(heightMeasureSpec,
                            getPaddingTopWithForeground() 
							+ getPaddingBottomWithForeground() 
                        	+ lp.topMargin + lp.bottomMargin,
                            lp.height);
                }

                child.measure(childWidthMeasureSpec, childHeightMeasureSpec);
            }
        }
	}


# View的布局流程 #
**ViewRootImpl.java**

	private void performLayout(WindowManager.LayoutParams lp, int desiredWindowWidth,
            int desiredWindowHeight) {
		...
		final View host = mView;
		...
		host.layout(0, 0, host.getMeasuredWidth(), host.getMeasuredHeight());
		...
	}

**View.java**

	//DecorView中 l=0, t=0, r=getMeasuredWidth(), b=getMeasuredHeight()
	//先执行setFrame确定此View控件在父容器中的布局位置，再执行onLayout方法设置子元素的布局位置（如果
		存在子元素View的话）
	public void layout(int l, int t, int r, int b) {
        ...
        int oldL = mLeft;
        int oldT = mTop;
        int oldB = mBottom;
        int oldR = mRight;

		//通常会执行setFrame方法
        boolean changed = isLayoutModeOptical(mParent) ?
                setOpticalFrame(l, t, r, b) : setFrame(l, t, r, b);

        if (changed || (mPrivateFlags & PFLAG_LAYOUT_REQUIRED) == PFLAG_LAYOUT_REQUIRED) {

            onLayout(changed, l, t, r, b);
            ...
            mPrivateFlags &= ~PFLAG_LAYOUT_REQUIRED;

            ListenerInfo li = mListenerInfo;
            if (li != null && li.mOnLayoutChangeListeners != null) {
                ArrayList<OnLayoutChangeListener> listenersCopy =
                        (ArrayList<OnLayoutChangeListener>)li.mOnLayoutChangeListeners.clone();
                int numListeners = listenersCopy.size();
                for (int i = 0; i < numListeners; ++i) {
                    listenersCopy.get(i).onLayoutChange(this, l, t, r, b, 
						oldL, oldT, oldR, oldB);
                }
            }
        }
        ...
    }

	//DecorView中 left=0, top=0, right=getMeasuredWidth(), bottom=getMeasuredHeight()
	//setFrame方法用来确定此View控件在父容器控件中的布局位置（即初始化mLeft,mTop,mRight,mBotton
		这四个值），于是就可以知道此View控件在视图坐标系中的位置，左上角顶点坐标为（mLeft,mTop），
		右下角顶点坐标为（mRight,mBottom）
	protected boolean setFrame(int left, int top, int right, int bottom) {
        boolean changed = false;

        if (DBG) {
            Log.d("View", this + " View.setFrame(" + left + "," + top + ","
               		 + right + "," + bottom + ")");
        }

        if (mLeft != left || mRight != right || mTop != top || mBottom != bottom) {
            changed = true;

            // Remember our drawn bit
            int drawn = mPrivateFlags & PFLAG_DRAWN;

            int oldWidth = mRight - mLeft;
            int oldHeight = mBottom - mTop;
            int newWidth = right - left;
            int newHeight = bottom - top;
            boolean sizeChanged = (newWidth != oldWidth) || (newHeight != oldHeight);

            // Invalidate our old position
            invalidate(sizeChanged);

            mLeft = left;
            mTop = top;
            mRight = right;
            mBottom = bottom;
            mRenderNode.setLeftTopRightBottom(mLeft, mTop, mRight, mBottom);

            mPrivateFlags |= PFLAG_HAS_BOUNDS;


            if (sizeChanged) {
                sizeChange(newWidth, newHeight, oldWidth, oldHeight);
            }
            ...
        }
        return changed;
    }

	protected void onSizeChanged(int w, int h, int oldw, int oldh) {}

	//DecorView中 left=0, top=0, right=getMeasuredWidth(), bottom=getMeasuredHeight()
	//在自定义容器控件时才需要重写onLayout方法，因为onLayout方法的作用是用来确定子元素控件在容器
		中的位置的。（容器控件在父容器中的位置是在该容器控件的layout方法中调用setFrame方法来确定的）
	protected void onLayout(boolean changed, int left, int top, int right, int bottom) {
    }

# View的绘制流程 #