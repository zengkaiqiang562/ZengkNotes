## android.support.v7.widget.RecyclerView ##

	public class RecyclerView extends ViewGroup 
			implements ScrollingView, NestedScrollingChild2

	public static final long NO_ID = -1;

	public void setAdapter(Adapter adapter)

	public void setLayoutManager(LayoutManager layout)

	public void setItemAnimator(ItemAnimator animator)

	public void addItemDecoration(ItemDecoration decor, int index)

	public void addItemDecoration(ItemDecoration decor) {
        addItemDecoration(decor, -1); //负数表示添加到最后
    }

### android.support.v7.widget.RecyclerView.Adapter<VH> ###

	public abstract static class Adapter<VH extends ViewHolder>

	public abstract VH onCreateViewHolder(ViewGroup parent, int viewType);

	public void onBindViewHolder(VH holder, int position, List<Object> payloads) {
        onBindViewHolder(holder, position);
    }

	public abstract void onBindViewHolder(VH holder, int position);

	public void onViewRecycled(VH holder) {}

	public abstract int getItemCount();

	public int getItemViewType(int position) {
        return 0;  
		//integer value identifying the type of the view needed to represent 	
			the item at position
		//Consider using id resources to uniquely identify item view types
    }
		//返回值会作为onCreateViewHolder的参数viewType

	public long getItemId(int position) {
        return NO_ID;  //return the stable ID of the item at position
    }
		//重写此方法时，返回参数position即可。但是当hasStableIds方法返回false时，此方法应该要
			返回NO_ID。即 return hasStableIds() ？ position : NO_ID;

	public void setHasStableIds(boolean hasStableIds) {
        if (hasObservers()) {
            throw new IllegalStateException("Cannot change whether this adapter has "
                	+ "stable IDs while the adapter has registered observers.");
        }
        mHasStableIds = hasStableIds;
    }

	public final boolean hasStableIds() {
        return mHasStableIds;
    }

	
	-------------添加Adapter的数据集的改变-------------------
	public void registerAdapterDataObserver(AdapterDataObserver observer)
	public void unregisterAdapterDataObserver(AdapterDataObserver observer)

	-----------通知Adapter的数据集的改变-------------------
	public final void notifyDataSetChanged() {
        mObservable.notifyChanged();
    }

	public final void notifyItemChanged(int position) {
        mObservable.notifyItemRangeChanged(position, 1);
    }
	
	//相当于调用notifyItemRangeChanged(position, itemCount, null)
	public final void notifyItemRangeChanged(int positionStart, int itemCount) {
        mObservable.notifyItemRangeChanged(positionStart, itemCount);
    }

	public final void notifyItemChanged(int position, Object payload) {
        mObservable.notifyItemRangeChanged(position, 1, payload);
    }

	public final void notifyItemRangeChanged(int positionStart, int itemCount, 
		Object payload) {

        mObservable.notifyItemRangeChanged(positionStart, itemCount, payload);
    }

	public final void notifyItemInserted(int position) {
        mObservable.notifyItemRangeInserted(position, 1);
    }

	public final void notifyItemRangeInserted(int positionStart, int itemCount) {
        mObservable.notifyItemRangeInserted(positionStart, itemCount);
    }

	public final void notifyItemMoved(int fromPosition, int toPosition) {
        mObservable.notifyItemMoved(fromPosition, toPosition);
    }

	public final void notifyItemRemoved(int position) {
        mObservable.notifyItemRangeRemoved(position, 1);
    }

	public final void notifyItemRangeRemoved(int positionStart, int itemCount) {
        mObservable.notifyItemRangeRemoved(positionStart, itemCount);
    }

### android.support.v7.widget.RecyclerView.ViewHolder ###

	public abstract static class ViewHolder

	public final View itemView;
	int mPosition = NO_POSITION;
	int mPreLayoutPosition = NO_POSITION;
    long mItemId = NO_ID;
    int mItemViewType = INVALID_TYPE;
	RecyclerView mOwnerRecyclerView; 
		//如果onBindViewHolder被调用，那么参数holder中的mOwnerRecyclerView是当前RecyclerView，
			如果执行完onViewRecycled方法后，onViewRecycled方法的参数holder中的
			mOwnerRecyclerView会被设置为null

	public ViewHolder(View itemView) {
        if (itemView == null) {
            throw new IllegalArgumentException("itemView may not be null");
        }
        this.itemView = itemView;
    }

	public final long getItemId() {
        return mItemId;
    }

    public final int getItemViewType() {
        return mItemViewType;
    }

	public final int getLayoutPosition() {
        return mPreLayoutPosition == NO_POSITION ? mPosition : mPreLayoutPosition;
    }

	public final int getAdapterPosition() {
        if (mOwnerRecyclerView == null) {
            return NO_POSITION;
        }
        return mOwnerRecyclerView.getAdapterPositionFor(this);
    }

### android.support.v7.widget.RecyclerView.LayoutManager ###

	public abstract static class LayoutManager

	public abstract LayoutParams generateDefaultLayoutParams();

	public void onLayoutChildren(Recycler recycler, State state) {
        Log.e(TAG, "You must override onLayoutChildren(Recycler recycler, State state) ");
    }

	public void detachAndScrapView(View child, Recycler recycler) {
        int index = mChildHelper.indexOfChild(child);
        scrapOrRecycleView(recycler, index, child);
    }

	public void removeAndRecycleView(View child, Recycler recycler) {
        removeView(child);
        recycler.recycleView(child);
    }

### android.support.v7.widget.RecyclerView.LayoutParams ###

	public static class LayoutParams extends android.view.ViewGroup.MarginLayoutParams

### android.support.v7.widget.RecyclerView.ItemAnimator ###

	public abstract static class ItemAnimator

	public abstract boolean animateDisappearance(@NonNull ViewHolder viewHolder,
                @NonNull ItemHolderInfo preLayoutInfo, 
				@Nullable ItemHolderInfo postLayoutInfo);

	public abstract boolean animateAppearance(@NonNull ViewHolder viewHolder,
                @Nullable ItemHolderInfo preLayoutInfo, 
				@NonNull ItemHolderInfo postLayoutInfo);

	public abstract boolean animatePersistence(@NonNull ViewHolder viewHolder,
                @NonNull ItemHolderInfo preLayoutInfo, 
				@NonNull ItemHolderInfo postLayoutInfo);

	public abstract boolean animateChange(@NonNull ViewHolder oldHolder,
                @NonNull ViewHolder newHolder,
                @NonNull ItemHolderInfo preLayoutInfo, 
				@NonNull ItemHolderInfo postLayoutInfo);

	public abstract void runPendingAnimations();

	public abstract void endAnimation(ViewHolder item);

	public abstract void endAnimations();

	public abstract boolean isRunning();

#### android.support.v7.widget.RecyclerView.ItemAnimator.ItemHolderInfo ####

	public static class ItemHolderInfo

	public int left;
	public int top;
	public int right;
	public int bottom;

	public ItemHolderInfo()

	public ItemHolderInfo setFrom(RecyclerView.ViewHolder holder) {
        return setFrom(holder, 0);
    }

	public ItemHolderInfo setFrom(RecyclerView.ViewHolder holder,
                    @AdapterChanges int flags) {
        final View view = holder.itemView;
        this.left = view.getLeft();
        this.top = view.getTop();
        this.right = view.getRight();
        this.bottom = view.getBottom();
        return this;
    }

### android.support.v7.widget.RecyclerView.ItemDecoration ###

	public abstract static class ItemDecoration

	//重写此方法即可
	public void onDraw(Canvas c, RecyclerView parent, State state) {
        onDraw(c, parent);
    }

	@Deprecated
    public void onDraw(Canvas c, RecyclerView parent) {}

	////重写此方法即可
	public void onDrawOver(Canvas c, RecyclerView parent, State state) {
        onDrawOver(c, parent);
    }

	@Deprecated
    public void onDrawOver(Canvas c, RecyclerView parent) {}

	public void getItemOffsets(Rect outRect, View view, RecyclerView parent, State state) {
        getItemOffsets(outRect, 
			((LayoutParams) view.getLayoutParams()).getViewLayoutPosition(),
            parent);
    }

	@Deprecated
    public void getItemOffsets(Rect outRect, int itemPosition, RecyclerView parent) {
        outRect.set(0, 0, 0, 0);
    }

### android.support.v7.widget.RecyclerView.AdapterDataObserver ###

	public abstract static class AdapterDataObserver

	public void onChanged() {}

    public void onItemRangeChanged(int positionStart, int itemCount) {}

    public void onItemRangeChanged(int positionStart, int itemCount, Object payload) {}

    public void onItemRangeInserted(int positionStart, int itemCount) {}

    public void onItemRangeRemoved(int positionStart, int itemCount) {}

    public void onItemRangeMoved(int fromPosition, int toPosition, int itemCount) {}

## android.support.v7.widget.LinearLayoutManager ##

	public class LinearLayoutManager extends RecyclerView.LayoutManager implements
        ItemTouchHelper.ViewDropHandler, RecyclerView.SmoothScroller.ScrollVectorProvider

    public static final int HORIZONTAL = OrientationHelper.HORIZONTAL;

    public static final int VERTICAL = OrientationHelper.VERTICAL;

	public LinearLayoutManager(Context context, int orientation, boolean reverseLayout)

	public LinearLayoutManager(Context context) {
        this(context, VERTICAL, false);
    }

## android.support.v7.widget.GridLayoutManager ##

	public class GridLayoutManager extends LinearLayoutManager

	public GridLayoutManager(Context context, int spanCount)

	public GridLayoutManager(Context context, int spanCount, int orientation,
            boolean reverseLayout)

## android.support.v7.widget.StaggeredGridLayoutManager ##

	public class StaggeredGridLayoutManager extends RecyclerView.LayoutManager implements
        RecyclerView.SmoothScroller.ScrollVectorProvider

    public static final int HORIZONTAL = OrientationHelper.HORIZONTAL;

    public static final int VERTICAL = OrientationHelper.VERTICAL;

	public StaggeredGridLayoutManager(int spanCount, int orientation)
	
## android.support.v7.widget.SimpleItemAnimator ##

	public abstract class SimpleItemAnimator extends RecyclerView.ItemAnimator

	public abstract boolean animateRemove(ViewHolder holder);

	public abstract boolean animateAdd(ViewHolder holder);

	public abstract boolean animateMove(ViewHolder holder, int fromX, int fromY,
            int toX, int toY);

	public abstract boolean animateChange(ViewHolder oldHolder,
            ViewHolder newHolder, int fromLeft, int fromTop, int toLeft, int toTop);

## android.support.v7.widget.DefaultItemAnimator ##

	public class DefaultItemAnimator extends SimpleItemAnimator

## android.support.v7.widget.DividerItemDecoration ##

	public class DividerItemDecoration extends RecyclerView.ItemDecoration

    public static final int HORIZONTAL = LinearLayout.HORIZONTAL;
    public static final int VERTICAL = LinearLayout.VERTICAL;

	public DividerItemDecoration(Context context, int orientation)

	public void setDrawable(@NonNull Drawable drawable)

## android.support.v7.widget.helper.ItemTouchHelper ##

	public class ItemTouchHelper extends RecyclerView.ItemDecoration
        implements RecyclerView.OnChildAttachStateChangeListener
		
	public static final int UP = 1;
    public static final int DOWN = 1 << 1;
    public static final int LEFT = 1 << 2;
    public static final int RIGHT = 1 << 3;
    
    public static final int START = LEFT << 2;
    public static final int END = RIGHT << 2;

    public static final int ACTION_STATE_IDLE = 0;
    public static final int ACTION_STATE_SWIPE = 1;
    public static final int ACTION_STATE_DRAG = 2;

	public ItemTouchHelper(Callback callback)

### android.support.v7.widget.helper.ItemTouchHelper.Callback ###

	public abstract static class Callback

	public abstract int getMovementFlags(RecyclerView recyclerView,
                ViewHolder viewHolder);

	public abstract boolean onMove(RecyclerView recyclerView,
                ViewHolder viewHolder, ViewHolder target);

	public abstract void onSwiped(ViewHolder viewHolder, int direction);