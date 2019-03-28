# Intent #

## Intent 打开浏览器 ##

	Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse("http://www.baidu.com"));
	context.startActivity(intent);

# Activity #

## 对话框风格的Activity ##
为Activity添加Dialog风格的主题，就能以显示对话框的方式来显示Activity

	<activity android:name=".MainActivity"
            android:theme="@android:style/Theme.Material.Light.Dialog">

	...

## Window.requestFeature(int featureId) ##
Activity通过requestWindowFeature(int featureId)方法设置Window的扩展特征，实际上就是直接调
getWindow.requestFeature(featureId)实现的

	public final boolean requestWindowFeature(int featureId) {
        return getWindow().requestFeature(featureId);
    }

requestWindowFeature方法设置Window特征，必须在调setContentView之前执行，并且可以调用多次
requestWindowFeature来设置多个Window特征。一旦设置后，Window特征就改变不了了。

可以设置的featureId如下：

	Window.FEATURE_ACTION_BAR  对于没有打开ActionBar的主题，在当前Activity窗口添加ActionBar

	Window.FEATURE_NO_TITLE  对于显示了标题栏TitleBar的主题，此特征值可以关闭标题栏


# Fragment， ViewPager #
Fragment是Android 3.0 （API 11） 新增的API。Fragment本意是片段的意思，相当于是Activity的一个模块
化区域。
所以，我们可以使用一个Activity组合多个Fragment，实现在一个Activity窗口中创建多个用户界面。
另外，多个Activity也可以复用一个Fragment。

Fragment有自己的生命周期方法；
Fragment可以定义自己的布局文件

开发一个Fragment时需要继承Fragment，并重写Fragment的生命周期方法，通常会重写
public View onCreateView(LayoutInflater, ViewGroup, Bundle)方法来加载Fragment的布局文件

为了兼容Android 3.0 之前的版本，自定义Fragment时需要继承android.support.v4.app.Fragment，而不是
继承android.app.Fragment。

在v4包中还为Fragment提供了如下配套类

	FragmentActivity： 创建Fragment时，需要先获取到FragmentManager，对于早期的版本，无法通过
		Activity.getFragmentManager()获取。于是v4包提供了FragmentActivity，早期版本中只有通过
			FragmentActivity.getSupportFragmentManager()方法才能获取FragmentManager管理器。

	ViewPager： 是Fragment的容器，可以同时管理多个Fragment，并为Fragment切换时提供动画效果

	FragmentPagerAdapter： ViewPager相当于ListView，FragmentPagerAdapter相当于BaseAdapter，而
			Fragment相当于ListView中的列表项。于是，就相当于通过BaseAdapter为ListView提供多个列表
			项那样，通过FragmentPagerAdapter为ViewPager提供多个Fragment。

	PagerTitleStrip： 于ViewPager结合使用，在ViewPager上显示导航条

以上介绍的类都位于v4包中，实际开发时建议使用v4中的Fragment及其相关类，以便兼容早期版本。