# RecyclerView #
RecyclerView是Android 5.0（API 21）新增的组件，为了兼容低版本建议使用support-v7包中RecyclerView。

所以使用RecyclerView需要导入依赖：

	compile "com.android.support:recyclerview-v7:21.0.2"

Flexible（可扩展性）是RecyclerView的特点

横向滚动的ListView、横向滚动的GridView、瀑布流控件这些功能都可以通过RecyclerView实现。

RecyclerView封装了ViewHolder的回收复用，编写RecyclerView.Adapter时面向的是ViewHolder，而不再
是View。

RecyclerView通过LayoutManager为item设置布局方式，通过ItemAnimator为item添加增删动画，
通过ItemDecoration为item添加间隔样式。
最后，通过RecyclerView.Adapter为RecyclerView提供item。item被封装在ViewHolder中。

需要注意的是，RecyclerView不像ListView那样提供了监听item点击事件的API。要想在RecyclerView中
监听item的点击事件，需要手动将监听器直接绑定到item中的视图View上。（可以在自定义ViewHolder
中操作）

一个ViewHolder实例中包括了一个item的根视图itemView，item的位置mPosition，item的viewType类型
mViewType。
并且当item绑定到RecyclerView中时，ViewHolder会持有当前的RecyclerView实例 —— mOwnerRecyclerView

## 设置 RecyclerView的布局管理器 LayoutManager ##
为RecyclerView设置布局管理器LayoutManager，即可控制item的布局方式，如横向、纵向、以及瀑布流

	public void setLayoutManager(LayoutManager layout)

Android提供了LinearLayoutManager、GridLayoutManager、StaggeredGridLayoutManager这三个
LayoutManager的子类作为可以直接使用的布局管理器。其中LinearLayoutManager布局管理器用于实现
ListView的效果；GridLayoutManager用于实现GridView的效果；StaggeredGridLayoutManager用于实现
瀑布流的效果。

	public LinearLayoutManager(Context context, int orientation, boolean reverseLayout)

	public GridLayoutManager(Context context, int spanCount, int orientation,
            boolean reverseLayout)

	public StaggeredGridLayoutManager(int spanCount, int orientation)

如上是这三个布局管理器各自提供的构造方法。其中参数orientation表示item的布局方向。横向滑动时
设置为OrientationHelper.HORIZONTAL、纵向滑动时设置为OrientationHelper.VERTICAL

## 设置 RecyclerView中item的增删动画 ItemAnimator ##
	public void setItemAnimator(ItemAnimator animator)

ItemAnimator类是一个抽象类，用来实现item增删的动画效果。
通过重写ItemAnimator提供的方法，即可实现各种增删动画。
Android提供了一个默认的动画效果实现类DefaultItemAnimator。

## 设置 RecyclerView中item的间隔样式 ItemDecoration ##
	public void addItemDecoration(ItemDecoration decor, int index)

ItemDecoration是一个抽象类，用来在item之间添加装饰图形。
重写此类的相关抽象方法即可在item之间添加不同的装饰图形，但要注意图形放置的位置跟item的布局密切相关。

Android提供了一个DividerItemDecoration间隔样式，但此样式只适用于LinearLayoutManager布局方式

	public DividerItemDecoration(Context context, int orientation)
		//orientation表示LinearLayoutManager中item的布局方向

	public void setDrawable(@NonNull Drawable drawable)
		//设置装饰图形

## RecyclerView.Adapter<VH>  ##

	public abstract static class Adapter<VH extends ViewHolder>

如上是RecyclerView.Adapter的声明，所以自定义Adapter适配器继承自RecyclerView.Adapter时，需要
指定此Adapter所操作的ViewHolder类型。

**ViewHolder的使用**
ViewHolder类的声明如下：
	
	public abstract static class ViewHolder
ViewHolder被定义为一个抽象类，但没有提供抽象方法。这是因为ViewHolder中只持有对item的根视图View实例
的一个引用。此外，我们还需要通过itemView.findViewById对item的根视图下需要设置数据的View控件进行
初始化。于是，我们就可以通过自定义ViewHolder的子类，在子类的构造方法中实现。

**RecyclerView.Adapter中需要重写的方法**

	public abstract VH onCreateViewHolder(ViewGroup parent, int viewType);
		//在此方法中创建一个自定义的ViewHolder实例，
			在通过LayoutInflater创建ViewHolder实例中item的根视图itemView时，调用inflate方法
			时应为itemView指定父容器为参数parent，即：
				LayoutInflater inflater = LayoutInflater.from(parent.getContext());
				inflater.inflate(R.layout.item, parent, false);

	public void onBindViewHolder(VH holder, int position, List<Object> payloads) {
        onBindViewHolder(holder, position);
    }

	public abstract void onBindViewHolder(VH holder, int position);
		//通常只需重写此方法即可，在此方法中，为参数holder中的item的视图View控件设置数据，在此方法
			中可为item中的视图View控件设置点击事件

	public void onViewRecycled(VH holder) {}
		//此方法在参数holder被回收时调用

	public abstract int getItemCount();
		//此方法返回Adapter中所持有的item的总数

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

## 为RecyclerView添加HeaderView 和FooterView ##

## RecyclerView.Adapter 的数据集改变的通知 ##
Adapter数据集改变的通知分两种：
- 第一种是item的内容没有改变，仅仅是item的位置发生了改变（如插入新item
、删除已存在item、已存在item之间相互交换位置）；
- 第二种就是item的位置没有改变，仅仅是item的内容改变。

针对以上数据集的改变场景，RecyclerView.Adaper提供了如下几个通知方法：

	public final void notifyDataSetChanged() { 
        mObservable.notifyChanged();
    }
		//不管第一种，还是第二种改变场景，只要数据集有变化，都会通知到。

	public final void notifyItemChanged(int position) {
        mObservable.notifyItemRangeChanged(position, 1);
    }
	
	
	public final void notifyItemRangeChanged(int positionStart, int itemCount) {
        mObservable.notifyItemRangeChanged(positionStart, itemCount);
    }
		//相当于调用notifyItemRangeChanged(position, itemCount, null)

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

# RecyclerView 源码分析 #

## LayoutManager ##
当你需要从一个可能再生的前子视图中 回收旧的 view 或者 获取新的 view 时， 你的 LayoutManager 可以访问一个 Recycler 实例。
Recycler 也免掉了直接访问 view 当前适配器方法的麻烦。当你的 LayoutManager 需要一个新的子视图时，只要调用 getViewForPosition() 这个方法，Recycler 会决定到底是从头创建一个新的视图 还是重用一个已存在的废弃视图。 你的 LayoutManager 需要及时将不再显示的视图传递给 Recycler， 避免 Recycler 创建不必要的 view 对象。

布局更新时有两个方法处理已存在的子视图：detach 和 remove (分离和移除)。Detach 是一个轻量的记录 view 操作。 被 detach 的视图在你的代码返回前能够重新连接。可以通过 Recycler 在不 重新绑定/重新构建 子视图的情况下修改已连接子视图的索引。
Remove 意味着这个 view 已经不需要了。任何被永久移除的 view 都应该 放到 Recycler 中，方便以后重用，不过 API 并没有强制要求。 被 remove 的视图是否被回收取决于你。

Recycler 有两级视图缓存系统： scrap heap 和 recycle pool (垃圾堆和回收池)， Scrap heap 是一个轻
量的集合，视图可以不经过适配器直接返回给 LayoutManager 。通常被 detach 但会在同一布局重新使用的视图
会临时储存在这里。Recycle pool 存放的 是那些假定并没有得到正确数据(相应位置的数据)的视图， 因此它们
都要经过适配器重新绑定后才能返回给 LayoutManager。

当要给 LayoutManager 提供一个新 view 时，Recycler 首先会 检查 scrap heap 有没有对应的 
position/id；如果有对应的内容， 就直接返回数据不需要通过适配器重新绑定。如果没有的话， 
Recycler 就会从 recycle pool 里弄一个合适的视图出来， 然后用 adapter 给它绑定必要的数据 (就是
调用 RecyclerView.Adapter.bindViewHolder()) 再返回。 如果 recycle pool 中也不存在有效 view ，
就会在绑定数据前 创建新的 view (就是 RecyclerView.Adapter.createViewHolder())， 最后返回数据。

onLayoutChildren() 是 LayoutManager 的主入口。
