##Glide
Glide v4官方文档：https://muyangmin.github.io/glide-docs-cn/

Glide v4跟v3的区别较大！

Glide v3参考博客：https://www.jianshu.com/p/7ce7b02988a4

----

Glide，一个被google所推荐的图片加载库

Glide 对于 Android SDK 的最低要求是 API level 10（Android 2.3.3）

Glide滑行的意思，可以看出这个库的主旨就在于让图片加载变的流畅。现在被广泛使用，当然还是有很多开发者使用Square公司的picasso，也有两个库的对比 ： http://jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0327/2650.html

导入Glide：
dependencies{
	implementation 'com.github.bumptech.glide:glide:3.7.0'
    implementation 'com.android.support:support-v4:26.1.0'
}


##Glide v4相关API
**Glide**
public static RequestManager with(@NonNull Context context)
public static RequestManager with(@NonNull Activity activity)
public static RequestManager with(@NonNull FragmentActivity activity)
public static RequestManager with(@NonNull Fragment fragment)

**RequestManager**
public RequestBuilder<Drawable> load(@Nullable String string)
public RequestBuilder<Drawable> load(@Nullable URL url)
public RequestBuilder<Drawable> load(@Nullable Bitmap bitmap)
public RequestBuilder<Drawable> load(@Nullable Drawable drawable)
public RequestBuilder<Drawable> load(@RawRes @DrawableRes @Nullable Integer resourceId)
public RequestBuilder<Drawable> load(@Nullable File file)
public RequestBuilder<Drawable> load(@Nullable Uri uri)

**RequestBuilder<TranscodeType>**
public ViewTarget<ImageView, TranscodeType> into(@NonNull ImageView view)

