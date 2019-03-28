# AdapterView及其子类相关 API #

## android.widget.AdapterView<T> ##

	public abstract class AdapterView<T extends Adapter> extends ViewGroup

	public abstract T getAdapter();

	public abstract void setAdapter(T adapter);

	public void setOnItemClickListener(OnItemClickListener listener)

	public void setOnItemLongClickListener(OnItemLongClickListener listener)

	public void setOnItemSelectedListener(@Nullable OnItemSelectedListener listener)

## android.widget.AbsListView ##

	public abstract class AbsListView extends AdapterView<ListAdapter> implements 
		TextWatcher,
        ViewTreeObserver.OnGlobalLayoutListener, 
		Filter.FilterListener,
        ViewTreeObserver.OnTouchModeChangeListener,
        RemoteViewsAdapter.RemoteAdapterConnectionCallback

	public void setSelector(@DrawableRes int resID)

	public void setSelector(Drawable sel)

	public void setTextFilterEnabled(boolean textFilterEnabled)

	public void setFilterText(String filterText)

	public boolean hasTextFilter()

	public void clearTextFilter()

## android.widget.ListView ##

	public class ListView extends AbsListView

	public void setDivider(@Nullable Drawable divider)

	public void setDividerHeight(int height)

## android.widget.GridView ##

	public class GridView extends AbsListView

	public static final int NO_STRETCH = 0;
	public static final int STRETCH_SPACING = 1;
    public static final int STRETCH_COLUMN_WIDTH = 2;
    public static final int STRETCH_SPACING_UNIFORM = 3;

	public void setNumColumns(int numColumns)
	public void setColumnWidth(int columnWidth)

	public void setGravity(int gravity)

	public void setHorizontalSpacing(int horizontalSpacing)
	public void setVerticalSpacing(int verticalSpacing)

	public void setStretchMode(@StretchMode int stretchMode)

## android.widget.ExpandableListView ##

	public class ExpandableListView extends ListView

	public void setOnGroupCollapseListener(
            OnGroupCollapseListener onGroupCollapseListener)

	public void setOnGroupExpandListener(
            OnGroupExpandListener onGroupExpandListener)

	public void setOnGroupClickListener(OnGroupClickListener onGroupClickListener)

	public void setOnChildClickListener(OnChildClickListener onChildClickListener)

	public boolean collapseGroup(int groupPos)

	public boolean expandGroup(int groupPos)
	public boolean expandGroup(int groupPos, boolean animate)

	public void setAdapter(ExpandableListAdapter adapter)

## android.widget.Adapter ##

	void registerDataSetObserver(DataSetObserver observer);
	void unregisterDataSetObserver(DataSetObserver observer);

	boolean isEmpty();

	int getCount();
	Object getItem(int position);
	long getItemId(int position);
	View getView(int position, View convertView, ViewGroup parent);

## android.widget.ListAdapter ##

	public interface ListAdapter extends Adapter

	public boolean areAllItemsEnabled();

	boolean isEnabled(int position);

## android.widget.SpinnerAdapter ##

	public interface SpinnerAdapter extends Adapter

	public View getDropDownView(int position, View convertView, ViewGroup parent);

## android.widget.BaseAdapter ##

	public abstract class BaseAdapter implements ListAdapter, SpinnerAdapter

## android.widget.ArrayAdapter<T> ##

	public class ArrayAdapter<T> extends BaseAdapter implements 
		Filterable, ThemedSpinnerAdapter

	public @NonNull Filter getFilter()

	private class ArrayFilter extends Filter {...}

## android.widget.SimpleAdapter ##

	public class SimpleAdapter extends BaseAdapter implements 
		Filterable, ThemedSpinnerAdapter

	public void setViewBinder(ViewBinder viewBinder)

	public static interface ViewBinder {...}

	private class SimpleFilter extends Filter {...}

	public Filter getFilter()

## android.widget.SimpleAdapter.ViewBinder ##

	boolean setViewValue(View view, Object data, String textRepresentation);

## android.widget.Filter ##

	public abstract class Filter

	public final void filter(CharSequence constraint)

	public final void filter(CharSequence constraint, FilterListener listener)

	protected abstract FilterResults performFiltering(CharSequence constraint);

	protected abstract void publishResults(CharSequence constraint,
            FilterResults results);

	public static interface FilterListener {...}

## android.widget.Filter.FilterListener ##

	public void onFilterComplete(int count);

## android.widget.Filterable ##

	public interface Filterable

	Filter getFilter();


## android.app.ListActivity ##

	public class ListActivity extends Activity

	public void setListAdapter(ListAdapter adapter)

	public ListView getListView()

## android.widget.ExpandableListAdapter ##

	public interface ExpandableListAdapter

	void registerDataSetObserver(DataSetObserver observer);
	void unregisterDataSetObserver(DataSetObserver observer);

	int getGroupCount();

	int getChildrenCount(int groupPosition);

	Object getGroup(int groupPosition);

	Object getChild(int groupPosition, int childPosition);

	long getGroupId(int groupPosition);

	long getChildId(int groupPosition, int childPosition);

	boolean hasStableIds();

	View getGroupView(int groupPosition, boolean isExpanded, View convertView,
			ViewGroup parent);

	View getChildView(int groupPosition, int childPosition, boolean isLastChild,
			View convertView, ViewGroup parent);

	boolean isChildSelectable(int groupPosition, int childPosition);

	boolean areAllItemsEnabled();

	boolean isEmpty();

	void onGroupExpanded(int groupPosition);

	void onGroupCollapsed(int groupPosition);

	long getCombinedChildId(long groupId, long childId);

	long getCombinedGroupId(long groupId);

## android.widget.BaseExpandableListAdapter ##

	public abstract class BaseExpandableListAdapter implements ExpandableListAdapter, 
        HeterogeneousExpandableList

## android.widget.SimpleExpandableListAdapter ##

	public class SimpleExpandableListAdapter extends BaseExpandableListAdapter

## android.widget.AbsSpinner ##

	public abstract class AbsSpinner extends AdapterView<SpinnerAdapter>

	@attr ref android.R.styleable#AbsSpinner_entries

## android.widget.Spinner ##

	public class Spinner extends AbsSpinner implements OnClickListener

	public static final int MODE_DIALOG = 0;
	public static final int MODE_DROPDOWN = 1;

	@attr ref android.R.styleable#Spinner_dropDownSelector
	@attr ref android.R.styleable#Spinner_dropDownWidth
	@attr ref android.R.styleable#Spinner_gravity
	@attr ref android.R.styleable#Spinner_popupBackground
	@attr ref android.R.styleable#Spinner_prompt
	@attr ref android.R.styleable#Spinner_spinnerMode

	public void setDropDownWidth(int pixels)
	public void setGravity(int gravity)
	public void setPopupBackgroundDrawable(Drawable background)
	public void setPopupBackgroundResource(@DrawableRes int resId)
	public void setPrompt(CharSequence prompt)
	public void setPromptId(int promptResId)

	public void setDropDownVerticalOffset(int pixels)
	public void setDropDownHorizontalOffset(int pixels)

## android.widget.AdapterViewAnimator ##

	public abstract class AdapterViewAnimator extends AdapterView<Adapter>
        implements RemoteViewsAdapter.RemoteAdapterConnectionCallback, Advanceable

## android.widget.AdapterViewFlipper ##

	@RemoteView
	public class AdapterViewFlipper extends AdapterViewAnimator

## android.widget.StackView ##

	@RemoteView
	public class StackView extends AdapterViewAnimator

# ProgressBar及其子类 相关API #

## android.widget.ProgressBar ##

	@RemoteView
	public class ProgressBar extends View

	@attr ref android.R.styleable#ProgressBar_animationResolution
	@attr ref android.R.styleable#ProgressBar_indeterminate
	@attr ref android.R.styleable#ProgressBar_indeterminateBehavior
	@attr ref android.R.styleable#ProgressBar_indeterminateDrawable
	@attr ref android.R.styleable#ProgressBar_indeterminateDuration
	@attr ref android.R.styleable#ProgressBar_indeterminateOnly
	@attr ref android.R.styleable#ProgressBar_interpolator
	@attr ref android.R.styleable#ProgressBar_min
	@attr ref android.R.styleable#ProgressBar_max
	@attr ref android.R.styleable#ProgressBar_maxHeight
	@attr ref android.R.styleable#ProgressBar_maxWidth
	@attr ref android.R.styleable#ProgressBar_minHeight
	@attr ref android.R.styleable#ProgressBar_minWidth
	@attr ref android.R.styleable#ProgressBar_mirrorForRtl
	@attr ref android.R.styleable#ProgressBar_progress
	@attr ref android.R.styleable#ProgressBar_progressDrawable
	@attr ref android.R.styleable#ProgressBar_secondaryProgress

	public synchronized void setProgress(int progress)

	public synchronized final void incrementProgressBy(int diff) {
		setProgress(mProgress + diff);
	}
			

## android.widget.AbsSeekBar ##

	public abstract class AbsSeekBar extends ProgressBar

	public void setThumb(Drawable thumb)

## android.widget.SeekBar ##

	public class SeekBar extends AbsSeekBar

	@attr ref android.R.styleable#SeekBar_thumb

	public void setOnSeekBarChangeListener(OnSeekBarChangeListener l)

	public interface OnSeekBarChangeListener {...}

## android.widget.SeekBar.OnSeekBarChangeListener ##

	void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser);

	void onStartTrackingTouch(SeekBar seekBar);

	void onStopTrackingTouch(SeekBar seekBar);

## android.widget.RatingBar ##

	public class RatingBar extends AbsSeekBar

	@attr ref android.R.styleable#RatingBar_numStars
	@attr ref android.R.styleable#RatingBar_rating
	@attr ref android.R.styleable#RatingBar_stepSize
	@attr ref android.R.styleable#RatingBar_isIndicator

	public void setIsIndicator(boolean isIndicator)

	public void setOnRatingBarChangeListener(OnRatingBarChangeListener listener)

	public interface OnRatingBarChangeListener {...}

## android.widget.RatingBar.OnRatingBarChangeListener ##

	void onRatingChanged(RatingBar ratingBar, float rating, boolean fromUser);

# ViewAmimator 及其子类 #

## android.widget.ViewAnimator ##

	public class ViewAnimator extends FrameLayout

	@attr ref android.R.styleable#ViewAnimator_inAnimation
	@attr ref android.R.styleable#ViewAnimator_outAnimation
	@attr ref android.R.styleable#ViewAnimator_animateFirstView

	public void setInAnimation(Animation inAnimation)
	public void setInAnimation(Context context, @AnimRes int resourceID)

	public void setOutAnimation(Animation outAnimation)
	public void setOutAnimation(Context context, @AnimRes int resourceID)

	public void setAnimateFirstView(boolean animate)

	public void showNext()
	public void showPrevious()

	public void setDisplayedChild(int whichChild)

	public View getCurrentView()

	public void addView(View child, int index, ViewGroup.LayoutParams params)

	public void removeView(View view)

	public void removeViewAt(int index)

	public void removeViews(int start, int count)

## android.widget.ViewSwitcher ##

	public class ViewSwitcher extends ViewAnimator

	public View getNextView()

	public void setFactory(ViewFactory factory) {
        mFactory = factory;
        obtainView();
        obtainView();
    }

	public interface ViewFactory {...}

## android.widget.ViewSwitcher.ViewFactory ##

	View makeView();

## android.widget.ImageSwitcher ##

	public class ImageSwitcher extends ViewSwitcher

	public void setImageResource(@DrawableRes int resid) {
        ImageView image = (ImageView)this.getNextView();
        image.setImageResource(resid);
        showNext();
    }

	public void setImageURI(Uri uri)

	public void setImageDrawable(Drawable drawable)

## android.widget.TextSwitcher ##

	public class TextSwitcher extends ViewSwitcher

	public void setText(CharSequence text) {
        final TextView t = (TextView) getNextView();
        t.setText(text);
        showNext();
    }

	public void setCurrentText(CharSequence text) {
        ((TextView)getCurrentView()).setText(text);
    }

## android.widget.ViewFlipper ##

	public class ViewFlipper extends ViewAnimator

	@attr ref android.R.styleable#ViewFlipper_flipInterval
	@attr ref android.R.styleable#ViewFlipper_autoStart

	public void setFlipInterval(int milliseconds)

	public void setAutoStart(boolean autoStart)
	public boolean isAutoStart()

	public void startFlipping()
	public void stopFlipping()

	public boolean isFlipping()


# 其他控件 #

## android.widget.Toast ##

	public Toast(Context context)

	public Toast(@NonNull Context context, @Nullable Looper looper)

	public void show()

	public void cancel()

	public void setView(View view)

	public View getView()

	public void setDuration(@Duration int duration) //Toast.LENGTH_SHORT, Toast.LENGTH_LONG

	public void setMargin(float horizontalMargin, float verticalMargin)

	public void setGravity(int gravity, int xOffset, int yOffset)

	public WindowManager.LayoutParams getWindowParams()

	public void setText(CharSequence s)

	public void setText(@StringRes int resId)

	public static Toast makeText(Context context, CharSequence text, @Duration int duration)

	public static Toast makeText(Context context, @StringRes int resId, @Duration int duration)

	public static Toast makeText(@NonNull Context context, @Nullable Looper looper,
            @NonNull CharSequence text, @Duration int duration) {

        Toast result = new Toast(context, looper);

        LayoutInflater inflate = (LayoutInflater)
                context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        View v = inflate.inflate(com.android.internal.R.layout.transient_notification, null);
        TextView tv = (TextView)v.findViewById(com.android.internal.R.id.message);
        tv.setText(text);

        result.mNextView = v;
        result.mDuration = duration;

        return result;
    }

## android.widget.CalendarView ##

	public class CalendarView extends FrameLayout

## android.widget.DatePicker ##

	public class DatePicker extends FrameLayout

## android.widget.TimePicker ##

	public class TimePicker extends FrameLayout

## android.widget.NumberPicker ##

	public class NumberPicker extends LinearLayout

	public void setValue(int value)
	public int getValue()

	public void setMinValue(int minValue)
	public int getMinValue()

	public void setMaxValue(int maxValue)
	public int getMaxValue()

	public void setOnValueChangedListener(OnValueChangeListener onValueChangedListener)

	public interface OnValueChangeListener {...}

## android.widget.NumberPicker.OnValueChangeListener ##

	void onValueChange(NumberPicker picker, int oldVal, int newVal);

## android.widget.SearchView ##

	public class SearchView extends LinearLayout implements CollapsibleActionView

	private final SearchAutoComplete mSearchSrcTextView;

	@attr ref android.R.styleable#SearchView_iconifiedByDefault
	@attr ref android.R.styleable#SearchView_imeOptions
	@attr ref android.R.styleable#SearchView_inputType
	@attr ref android.R.styleable#SearchView_maxWidth
	@attr ref android.R.styleable#SearchView_queryHint

	public void setIconifiedByDefault(boolean iconified)

	public void setQueryHint(@Nullable CharSequence hint)

	public void setSubmitButtonEnabled(boolean enabled)

	public void setQuery(CharSequence query, boolean submit)

	public void setOnQueryTextListener(OnQueryTextListener listener)

	public interface OnQueryTextListener {...}

	public static class SearchAutoComplete extends AutoCompleteTextView {...}

## android.widget.SearchView.OnQueryTextListener ##

	boolean onQueryTextSubmit(String query);

	boolean onQueryTextChange(String newText);

## android.widget.TabHost ##

	public class TabHost extends FrameLayout implements 
		ViewTreeObserver.OnTouchModeChangeListener

	public void setup()

	public TabWidget getTabWidget()

	public FrameLayout getTabContentView()

	public TabSpec newTabSpec(@NonNull String tag)

	public void addTab(TabSpec tabSpec)

	public void clearAllTabs()

	public void setCurrentTab(int index)

	public int getCurrentTab()
	public String getCurrentTabTag()

	public View getCurrentTabView()  //current indicator view
	public View getCurrentView()  //current content view

	public void setOnTabChangedListener(OnTabChangeListener l)

	public interface OnTabChangeListener {...}

	public class TabSpec {...}

	private static interface IndicatorStrategy {...}

	private static interface ContentStrategy {...}

### android.widget.TabHost.TabSpec ###

	private IndicatorStrategy mIndicatorStrategy;
	private ContentStrategy mContentStrategy;

	public TabSpec setIndicator(CharSequence label)
	public TabSpec setIndicator(CharSequence label, Drawable icon)
	public TabSpec setIndicator(View view)

	public TabSpec setContent(int viewId)
	public TabSpec setContent(TabContentFactory contentFactory)
	public TabSpec setContent(Intent intent)

	public String getTag()

### android.widget.TabHost.IndicatorStrategy ###

	View createIndicatorView();

### android.widget.TabHost.ContentStrategy ###

	View getContentView();

### android.widget.TabHost.OnTabChangeListener ###

	void onTabChanged(String tabId); //tabId is getCurrentTabTag()

## android.widget.TabWidget ##

	public class TabWidget extends LinearLayout implements OnFocusChangeListener

	@attr ref android.R.styleable#TabWidget_divider
	@attr ref android.R.styleable#TabWidget_tabStripEnabled
	@attr ref android.R.styleable#TabWidget_tabStripLeft
	@attr ref android.R.styleable#TabWidget_tabStripRight

	public void setDividerDrawable(@Nullable Drawable drawable)
	public void setDividerDrawable(@DrawableRes int resId)

	public void setLeftStripDrawable(@Nullable Drawable drawable)
	public void setLeftStripDrawable(@DrawableRes int resId)

	public void setRightStripDrawable(@Nullable Drawable drawable)
	public void setRightStripDrawable(@DrawableRes int resId)

	public void setStripEnabled(boolean stripEnabled)	

	public View getChildTabViewAt(int index)
	public int getTabCount()

	public void focusCurrentTab(int index)

## android.widget.ScrollView ##

	public class ScrollView extends FrameLayout

	@attr ref android.R.styleable#ScrollView_fillViewport

	public void setFillViewport(boolean fillViewport)

## android.widget.HorizontalScrollView ##

	public class HorizontalScrollView extends FrameLayout

	@attr ref android.R.styleable#HorizontalScrollView_fillViewport

	public void setFillViewport(boolean fillViewport)

# 通知 Notification #
## android.app.NotificationManager ##

	@SystemService(Context.NOTIFICATION_SERVICE)
	public class NotificationManager

	public static NotificationManager from(Context context) {
        return (NotificationManager) context.getSystemServic(
			Context.NOTIFICATION_SERVICE);
    }

	public void notify(int id, Notification notification)

	public void cancel(int id)

	public void cancelAll()

## android.app.Notification ##

	public class Notification implements Parcelable

	public static class Builder {...}
	

### android.app.Notification.Builder ###

	@Deprecated
	public Builder(Context context)

	public Builder(Context context, String channelId)

	public Builder setAutoCancel(boolean autoCancel)

	public Builder setTicker(CharSequence tickerText)

	public Builder setSmallIcon(@DrawableRes int icon)
	public Builder setSmallIcon(Icon icon)

	public Builder setLargeIcon(Bitmap b)
	public Builder setLargeIcon(Icon icon)

	public Builder setContentTitle(CharSequence title)

	public Builder setContentText(CharSequence text)

	public Builder setSubText(CharSequence text)

	@Deprecated
	public Builder setDefaults(int defaults)
			//Notification.DEFAULT_SOUND|DEFAULT_VIBRATE|DEFAULT_LIGHTS|DEFAULT_ALL

	@Deprecated
	public Builder setSound(Uri sound)

	@Deprecated
	public Builder setSound(Uri sound, int streamType)

	@Deprecated
	public Builder setVibrate(long[] pattern)

	public Builder setContentIntent(PendingIntent intent)
	
	public Builder setFullScreenIntent(PendingIntent intent, boolean highPriority)

	public Builder setWhen(long when)
	public Builder setShowWhen(boolean show)

	public Builder setCustomContentView(RemoteViews contentView)
	public Builder setCustomBigContentView(RemoteViews contentView)

	public Builder setVisibility(@Visibility int visibility)
		//Notification.VISIBILITY_PUBLIC|VISIBILITY_PRIVATE|VISIBILITY_SECRET

	public Notification build()
	
# RemoteViews #
## android.widget.RemoteViews ##

	public class RemoteViews implements Parcelable, Filter

	public RemoteViews(String packageName, int layoutId)

	public void setTextViewText(int viewId, CharSequence text)

	public void setImageViewResource(int viewId, int srcId)
	public void setImageViewBitmap(int viewId, Bitmap bitmap)

	public void setOnClickPendingIntent(int viewId, PendingIntent pendingIntent)

# PopupWindow #
## android.widget.PopupWindow ##

	public class PopupWindow

	public PopupWindow(View contentView, int width, int height)
	public PopupWindow(View contentView, int width, int height, boolean focusable)

	public void setBackgroundDrawable(Drawable background)

	public void setElevation(float elevation)
	
	public void setAnimationStyle(int animationStyle)
		//see com.android.internal.R.style.Animation_DropDownUp

	public void setContentView(View contentView)

	public View getContentView()
	
	public void showAtLocation(View parent, int gravity, int x, int y)

	public void showAsDropDown(View anchor, int xoff, int yoff)

	public void showAsDropDown(View anchor, int xoff, int yoff, int gravity)

	public void dismiss()


# Dialog 对话框 #

## android.app.Dialog ##

	public class Dialog implements DialogInterface, Window.Callback,
        KeyEvent.Callback, OnCreateContextMenuListener, Window.OnWindowDismissedCallback

	public void show()

## android.content.DialogInterface ##

	public interface DialogInterface

	int BUTTON_POSITIVE = -1;
	int BUTTON_NEGATIVE = -2;
	int BUTTON_NEUTRAL = -3;
	
	@Deprecated
    int BUTTON1 = BUTTON_POSITIVE;
    @Deprecated
    int BUTTON2 = BUTTON_NEGATIVE;
    @Deprecated
    int BUTTON3 = BUTTON_NEUTRAL;

	void cancel();

	void dismiss();

	interface OnCancelListener {...}

	interface OnDismissListener {...}

	interface OnShowListener {...}

	interface OnClickListener {...}

	interface OnMultiChoiceClickListener {...}
	
	interface OnKeyListener {...}

### android.content.DialogInterface.OnCancelListener ###

	void onCancel(DialogInterface dialog);

### android.content.DialogInterface.OnDismissListener ###

	void onDismiss(DialogInterface dialog);

### android.content.DialogInterface.OnShowListener ###

	void onShow(DialogInterface dialog);

### android.content.DialogInterface.OnClickListener ###

	void onClick(DialogInterface dialog, int which); 
		//which is the button clicked or the position of the item clicked

### android.content.DialogInterface.OnMultiChoiceClickListener ###

	void onClick(DialogInterface dialog, int which, boolean isChecked);

### android.content.DialogInterface.OnKeyListener ###

	boolean onKey(DialogInterface dialog, int keyCode, KeyEvent event);


## android.app.AlertDialog ##

	public class AlertDialog extends Dialog implements DialogInterface

	public Button getButton(int whichButton)

	public ListView getListView()

	public void setTitle(CharSequence title)

	public void setIcon(@DrawableRes int resId)

	public void setIcon(Drawable icon)

	public void setCustomTitle(View customTitleView)

	public void setMessage(CharSequence message)

	public void setView(View view)

	public void setButton(int whichButton, CharSequence text, OnClickListener listener)

	public static class Builder {...}

	
### android.app.AlertDialog.Builder ###

	public Builder(Context context)

	public Builder(Context context, int themeResId)

	public Builder setTitle(@StringRes int titleId)
	public Builder setTitle(CharSequence title)

	public Builder setCustomTitle(View customTitleView)

	public Builder setIcon(@DrawableRes int iconId)
	public Builder setIcon(Drawable icon)

	public Builder setMessage(@StringRes int messageId)
	public Builder setMessage(CharSequence message)

	public Builder setPositiveButton(@StringRes int textId, final OnClickListener listener)
	public Builder setPositiveButton(CharSequence text, final OnClickListener listener)

	public Builder setNegativeButton(@StringRes int textId, final OnClickListener listener)
	public Builder setNegativeButton(CharSequence text, final OnClickListener listener)

	public Builder setNeutralButton(@StringRes int textId, final OnClickListener listener)
	public Builder setNeutralButton(CharSequence text, final OnClickListener listener)

	public Builder setCancelable(boolean cancelable)
	
	public Builder setOnCancelListener(OnCancelListener onCancelListener)

	public Builder setOnDismissListener(OnDismissListener onDismissListener)

	public Builder setOnKeyListener(OnKeyListener onKeyListener)

	public Builder setItems(@ArrayRes int itemsId, final OnClickListener listener)
	public Builder setItems(CharSequence[] items, final OnClickListener listener)

	public Builder setAdapter(final ListAdapter adapter, final OnClickListener listener)

	public Builder setMultiChoiceItems(@ArrayRes int itemsId, boolean[] checkedItems,
                final OnMultiChoiceClickListener listener)

	public Builder setMultiChoiceItems(CharSequence[] items, boolean[] checkedItems,
                final OnMultiChoiceClickListener listener)

	public Builder setSingleChoiceItems(@ArrayRes int itemsId, int checkedItem,
                final OnClickListener listener)

	public Builder setSingleChoiceItems(CharSequence[] items, int checkedItem, 
				final OnClickListener listener)

	public Builder setSingleChoiceItems(ListAdapter adapter, int checkedItem, 
				final OnClickListener listener)

	public Builder setOnItemSelectedListener(
				final AdapterView.OnItemSelectedListener listener)

	public Builder setView(int layoutResId)
	public Builder setView(View view)

	public AlertDialog create()

	public AlertDialog show()

## android.app.ProgressDialog ##

	@Deprecated
	public class ProgressDialog extends AlertDialog

	public ProgressDialog(Context context)

	public ProgressDialog(Context context, int theme)

	public void setIndeterminate(boolean indeterminate)

	public void setMessage(CharSequence message)

	public void setProgress(int value)

	public void incrementProgressBy(int diff)

	public void setSecondaryProgress(int secondaryProgress)

	public void setMax(int max)

	public void setProgressStyle(int style) 
			//ProgressDialog.STYLE_SPINNER|STYLE_HORIZONTAL

	public static ProgressDialog show(Context context, CharSequence title,
            CharSequence message, boolean indeterminate)

# 菜单 Menu #

## android.view.Menu ##

	public interface Menu

	public MenuItem add(CharSequence title);
	public MenuItem add(@StringRes int titleRes);

	public MenuItem add(int groupId, int itemId, int order, CharSequence title);
		//groupId=Menu.NONE(0) 表示不存在于一个组中
		//order=Menu.NONE(0) 表示不关心排序
	public MenuItem add(int groupId, int itemId, int order, @StringRes int titleRes);

	SubMenu addSubMenu(final CharSequence title);
	SubMenu addSubMenu(@StringRes final int titleRes);

	SubMenu addSubMenu(final int groupId, final int itemId, int order, final CharSequence title);
	SubMenu addSubMenu(int groupId, int itemId, int order, @StringRes int titleRes);


	public MenuItem findItem(int id);

	public MenuItem getItem(int index); //index和findItem的参数id不一样，

	public void removeItem(int id);

	public void removeGroup(int groupId);

	public void clear();

	public int size();

	public void close();

	public void setGroupCheckable(int group, boolean checkable, boolean exclusive);

## android.view.SubMenu ##
	
	public interface SubMenu extends Menu

	public SubMenu setHeaderTitle(@StringRes int titleRes);
	public SubMenu setHeaderTitle(CharSequence title);

	public SubMenu setHeaderIcon(@DrawableRes int iconRes);
	public SubMenu setHeaderIcon(Drawable icon);

	public SubMenu setHeaderView(View view);

	public void clearHeader();

	public SubMenu setIcon(@DrawableRes int iconRes);
	public SubMenu setIcon(Drawable icon);

## android.view.ContextMenu ##

	public interface ContextMenu extends Menu

	public ContextMenu setHeaderTitle(@StringRes int titleRes);
	public ContextMenu setHeaderTitle(CharSequence title);

	public ContextMenu setHeaderIcon(@DrawableRes int iconRes);
	public ContextMenu setHeaderIcon(Drawable icon);

	public ContextMenu setHeaderView(View view);

	public void clearHeader();

## android.view.MenuItem ##

	public interface MenuItem

	public MenuItem setIntent(Intent intent);
	public Intent getIntent();

	public MenuItem setCheckable(boolean checkable);
	public boolean isCheckable();
	public MenuItem setChecked(boolean checked);
	public boolean isChecked();

	public boolean hasSubMenu();
	public SubMenu getSubMenu();

	public int getItemId();

	public int getGroupId();

	public void setShowAsAction(int actionEnum);
		//MenuItem.SHOW_AS_ACTION_ALWAYS|SHOW_AS_ACTION_IF_ROOM|
		//SHOW_AS_ACTION_NEVER|SHOW_AS_ACTION_WITH_TEXT
		//默认是SHOW_AS_ACTION_NEVER，即从不显示在ActionBar主体上，而是显示在溢出菜单中(三个点)

	public MenuItem setActionView(View view);

	public MenuItem setActionView(@LayoutRes int resId);

	public View getActionView();

	public MenuItem setOnMenuItemClickListener(
			MenuItem.OnMenuItemClickListener menuItemClickListener);

	public interface OnMenuItemClickListener {...}

### android.view.MenuItem.OnMenuItemClickListener ###

	public boolean onMenuItemClick(MenuItem item);

## android.widget.PopupMenu ##

	public class PopupMenu

	public PopupMenu(Context context, View anchor)

	public PopupMenu(Context context, View anchor, int gravity)

	public PopupMenu(Context context, View anchor, int gravity, int popupStyleAttr,
            int popupStyleRes)
		//popupStyleRes样式中可指定ListPopupWindow_dropDownHorizontalOffset 和
		//ListPopupWindow_dropDownVerticalOffset
		

	public MenuInflater getMenuInflater()

	public void inflate(@MenuRes int menuRes)

	public Menu getMenu()

	public void show()

	public void dismiss()

	public void setOnMenuItemClickListener(OnMenuItemClickListener listener)

	public void setOnDismissListener(OnDismissListener listener)
	
# ActionBar 活动条 #

## android.app.ActionBar ##

	public abstract class ActionBar

	public abstract void show();

	public abstract void hide();

	public abstract boolean isShowing();

	public abstract void setDisplayShowHomeEnabled(boolean showHome);

	public void setHomeButtonEnabled(boolean enabled)

	public abstract void setDisplayHomeAsUpEnabled(boolean showHomeAsUp);

	public abstract void setDisplayOptions(@DisplayOptions int options);

	public abstract void setDisplayOptions(@DisplayOptions int options, 
			@DisplayOptions int mask); 
		//setDisplayHomeAsUpEnabled(true) == setDisplayOptions(ActionBar.DISPLAY_HOME_AS_UP)
		//== setDisplayOptions(ActionBar.DISPLAY_HOME_AS_UP, ActionBar.DISPLAY_HOME_AS_UP);


	public abstract void setIcon(@DrawableRes int resId);
	public abstract void setIcon(Drawable icon);

	public abstract void setTitle(CharSequence title);
	public abstract void setTitle(@StringRes int resId);

	public void setHomeAsUpIndicator(Drawable indicator)
	public void setHomeAsUpIndicator(@DrawableRes int resId)

	public abstract void setCustomView(View view);
	public abstract void setCustomView(View view, LayoutParams layoutParams);
	public abstract void setCustomView(@LayoutRes int resId);
	
	public abstract View getCustomView();

	public abstract void setDisplayShowCustomEnabled(boolean showCustom);

	public abstract void setBackgroundDrawable(@Nullable Drawable d); //设置ActionBar整体背景

	public abstract void setNavigationMode(@NavigationMode int mode);
		//ActionBar.NAVIGATION_MODE_LIST|NAVIGATION_MODE_TABS

	public abstract void addTab(Tab tab);
	public abstract Tab newTab();

	public abstract void setListNavigationCallbacks(SpinnerAdapter adapter,
            OnNavigationListener callback);

	public static abstract class Tab {...}

	public interface TabListener {...}

	public interface OnNavigationListener {...}

### android.app.ActionBar.Tab ###
	
	ublic abstract Tab setText(CharSequence text);

	public abstract Tab setIcon(Drawable icon);

	public abstract Tab setCustomView(View view);
	public abstract View getCustomView();

	public abstract int getPosition();

	public abstract Tab setTabListener(TabListener listener);

### android.app.ActionBar.TabListener ###

	public void onTabSelected(Tab tab, FragmentTransaction ft);

	public void onTabUnselected(Tab tab, FragmentTransaction ft);

	public void onTabReselected(Tab tab, FragmentTransaction ft);

### android.app.ActionBar.OnNavigationListener ###

	public boolean onNavigationItemSelected(int itemPosition, long itemId);

# SurfaceView #
## android.view.SurfaceView ##

	public class SurfaceView extends View implements ViewRootImpl.WindowStoppedCallback

	private final SurfaceHolder mSurfaceHolder = new SurfaceHolder() {...}

	public SurfaceHolder getHolder()

## android.view.SurfaceHolder ##

	public interface SurfaceHolder

	public void addCallback(Callback callback);

	public void setFixedSize(int width, int height);

	public void setSizeFromLayout();

	public void setFormat(int format);
		//默认是PixelFormat.OPAQUE，SurfaceView会把OPAQUE当成PixelFormat.RGB_565

	public void setKeepScreenOn(boolean screenOn);

	public Rect getSurfaceFrame();

	public Surface getSurface();

	public Canvas lockCanvas();

	public Canvas lockCanvas(Rect dirty);
			//此方法和lockCanvas()方法返回的都是同一个Canvas对象，只不过对于带参的lockCanvas方法，
				SurfaceView只对Rect所圈出来的区域进行更新，此方式能提高画面的更新速度

	public void unlockCanvasAndPost(Canvas canvas);
			//调用此方法后，之前所绘制的图形还处于缓冲区，下一次lockCanvas方法锁定的区域可能会被
				还处于缓冲区的图形遮挡住。

	public interface Callback {...}

### android.view.SurfaceHolder.Callback ###

	public void surfaceCreated(SurfaceHolder holder);

	public void surfaceChanged(SurfaceHolder holder, int format, int width, int height);
		当SurfaceView的format格式或大小发生改变是回调

	public void surfaceDestroyed(SurfaceHolder holder);


# Android 5.0 新特性相关API #

## android.support.v7.graphics.Palette ##

	public final class Palette

	public Swatch getVibrantSwatch()

	public Swatch getLightVibrantSwatch()

	public Swatch getDarkVibrantSwatch()

	public Swatch getMutedSwatch()

	public Swatch getLightMutedSwatch()

	public Swatch getDarkMutedSwatch()

	public static Builder from(Bitmap bitmap) {
        return new Builder(bitmap);
    }

	public static final class Builder {...}

	public interface PaletteAsyncListener {...}

### android.support.v7.graphics.Palette.Builder ###

	public Builder(Bitmap bitmap)

	public Palette generate()

	public AsyncTask<Bitmap, Void, Palette> generate(final PaletteAsyncListener listener)

### android.support.v7.graphics.Palette.PaletteAsyncListener ###

	void onGenerated(Palette palette);

### android.support.v7.graphics.Palette.Swatch ###

	public int getRgb()

