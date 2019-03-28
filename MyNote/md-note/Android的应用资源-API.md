## android.content.Context ##

	public abstract class Context

	public abstract AssetManager getAssets();

	public abstract Resources getResources();

## android.content.res.Resources ##

	public class Resources

	public String getString(@StringRes int id)

	public String getString(@StringRes int id, Object... formatArgs)

	public Drawable getDrawable(@DrawableRes int id)

	public int getColor(@ColorRes int id)

	public String[] getStringArray(@ArrayRes int id)

	public int[] getIntArray(@ArrayRes int id)

	public TypedArray obtainTypedArray(@ArrayRes int id)

	public int getInteger(@IntegerRes int id)

	public float getDimension(@DimenRes int id)

	public boolean getBoolean(@BoolRes int id)

	public InputStream openRawResource(@RawRes int id) throws NotFoundException
			// 将res/raw/下的原生资源文件转成IO流的形式进行访问

	public AssetFileDescriptor openRawResourceFd(@RawRes int id) throws NotFoundException
			//返回结果可传递给MediaPlayer.setDataSource(AssetFileDescriptor)

	public DisplayMetrics getDisplayMetrics()

## android.content.res.AssetManager ##
	
	public final class AssetManager implements AutoCloseable

	public final InputStream open(String fileName) throws IOException
			//fileName就是main/assets/目录下的文件名，不需要带上路径

	public final AssetFileDescriptor openFd(String fileName) throws IOException
			//返回结果可传递给MediaPlayer.setDataSource(AssetFileDescriptor)

	public native final String[] list(String path) //返回相对于assets/目录的path路径下的所有文件
			//list("")表示返回assets/目录下的所有文件。
			//如果需要获取assets/child/目录下的所有文件，应执行 list("child")

## android.content.res.AssetFileDescriptor ##

	public class AssetFileDescriptor implements Parcelable, Closeable

	public FileInputStream createInputStream() throws IOException

	public FileOutputStream createOutputStream() throws IOException

	public FileDescriptor getFileDescriptor()

	public ParcelFileDescriptor getParcelFileDescriptor()

	public long getStartOffset()

	public long getLength()

	public void close() throws IOException


## android.util.DisplayMetrics ##

	public class DisplayMetrics

	public static final int DENSITY_MEDIUM = 160;

	public static final int DENSITY_DEFAULT = DENSITY_MEDIUM;

	public float density;  // dp/px在不同DPI下的比例值scale，如480DPI下：density=3

	public float scaledDensity; 
			//sp/px在不同DPI下的比例值scale，适用于文字大小，可能跟density一致

	public int densityDpi; //屏幕密度，如480DPI

	public int widthPixels; //屏幕宽度，单位px

	public int heightPixels; 
		//屏幕高度，单位px。如果屏幕底部显示了导航栏（NavigationBar），那么会减去NavigationBar的高度

## android.util.TypedValue ##

	public class TypedValue

	public static float applyDimension(int unit, float value, DisplayMetrics metrics)
		//unit可以是 TypedValue.COMPLEX_UNIT_DIP|COMPLEX_UNIT_SP，将对应单位的value转成px单位

## android.content.res.TypedArray ##

	public class TypedArray

	public Drawable getDrawable(@StyleableRes int index)

	public int getColor(@StyleableRes int index, @ColorInt int defValue)

	public String getString(@StyleableRes int index)

	public float getDimension(@StyleableRes int index, float defValue)

	public void recycle()

