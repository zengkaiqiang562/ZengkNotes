Android中的所有View视图都是添加到Window中，通过Window来呈现的。所以，Window是View的直接管理者

Android中Window是一个接口，其实现类是PhoneWindow

创建一个Window需要用到WindowManager

## android.view.WindowManager.LayoutParams ##

	public static class LayoutParams extends ViewGroup.LayoutParams implements Parcelable


	public int x;  //window的左上角顶点Android坐标系中x坐标
    public int y;  //window的左上角顶点Android坐标系中y坐标

	public int type;  //表示Window的类型，有如下三种。Window分层显示，层级大的会覆盖在层级小的上面
	
	-----------------应用Window  层级范围：[1, 99]---------------------
	public static final int TYPE_BASE_APPLICATION   = 1;
	public static final int TYPE_APPLICATION        = 2;
	public static final int TYPE_APPLICATION_STARTING = 3;
	public static final int TYPE_DRAWN_APPLICATION = 4;
    public static final int LAST_APPLICATION_WINDOW = 99;

	-----------------子Window  层级范围：[1000, 1999]------------------
	public static final int FIRST_SUB_WINDOW = 1000;
	public static final int TYPE_APPLICATION_PANEL = FIRST_SUB_WINDOW;
	public static final int TYPE_APPLICATION_MEDIA = FIRST_SUB_WINDOW + 1;
	public static final int TYPE_APPLICATION_SUB_PANEL = FIRST_SUB_WINDOW + 2;
	public static final int TYPE_APPLICATION_ATTACHED_DIALOG = FIRST_SUB_WINDOW + 3;
	public static final int TYPE_APPLICATION_MEDIA_OVERLAY  = FIRST_SUB_WINDOW + 4;
	public static final int TYPE_APPLICATION_ABOVE_SUB_PANEL = FIRST_SUB_WINDOW + 5;
	public static final int LAST_SUB_WINDOW = 1999;

	----------------系统Window  层级范围：[2000, 2999]---------------------
	public static final int FIRST_SYSTEM_WINDOW     = 2000;
	public static final int TYPE_STATUS_BAR         = FIRST_SYSTEM_WINDOW;
	public static final int TYPE_SEARCH_BAR         = FIRST_SYSTEM_WINDOW+1;
	@Deprecated
	public static final int TYPE_PHONE              = FIRST_SYSTEM_WINDOW+2;
	@Deprecated
	public static final int TYPE_SYSTEM_ALERT       = FIRST_SYSTEM_WINDOW+3;
	public static final int TYPE_KEYGUARD           = FIRST_SYSTEM_WINDOW+4;
	@Deprecated
	public static final int TYPE_TOAST              = FIRST_SYSTEM_WINDOW+5;
	@Deprecated
	public static final int TYPE_SYSTEM_OVERLAY     = FIRST_SYSTEM_WINDOW+6;
	@Deprecated
	public static final int TYPE_PRIORITY_PHONE     = FIRST_SYSTEM_WINDOW+7;
	public static final int TYPE_SYSTEM_DIALOG      = FIRST_SYSTEM_WINDOW+8;
	public static final int TYPE_KEYGUARD_DIALOG    = FIRST_SYSTEM_WINDOW+9;
	@Deprecated
	public static final int TYPE_SYSTEM_ERROR               = FIRST_SYSTEM_WINDOW+10;
	public static final int TYPE_INPUT_METHOD               = FIRST_SYSTEM_WINDOW+11;
	public static final int TYPE_INPUT_METHOD_DIALOG        = FIRST_SYSTEM_WINDOW+12;
	public static final int TYPE_WALLPAPER                  = FIRST_SYSTEM_WINDOW+13;
	public static final int TYPE_STATUS_BAR_PANEL           = FIRST_SYSTEM_WINDOW+14;
	public static final int TYPE_SECURE_SYSTEM_OVERLAY      = FIRST_SYSTEM_WINDOW+15;
	public static final int TYPE_DRAG                       = FIRST_SYSTEM_WINDOW+16;
	public static final int TYPE_STATUS_BAR_SUB_PANEL       = FIRST_SYSTEM_WINDOW+17;
	public static final int TYPE_POINTER                    = FIRST_SYSTEM_WINDOW+18;
	public static final int TYPE_NAVIGATION_BAR             = FIRST_SYSTEM_WINDOW+19;
	public static final int TYPE_VOLUME_OVERLAY             = FIRST_SYSTEM_WINDOW+20;
	public static final int TYPE_BOOT_PROGRESS              = FIRST_SYSTEM_WINDOW+21;
	public static final int TYPE_INPUT_CONSUMER             = FIRST_SYSTEM_WINDOW+22;
	public static final int TYPE_DREAM                      = FIRST_SYSTEM_WINDOW+23;
	public static final int TYPE_NAVIGATION_BAR_PANEL       = FIRST_SYSTEM_WINDOW+24;
	public static final int TYPE_DISPLAY_OVERLAY            = FIRST_SYSTEM_WINDOW+26;
	public static final int TYPE_MAGNIFICATION_OVERLAY      = FIRST_SYSTEM_WINDOW+27;
	public static final int TYPE_PRIVATE_PRESENTATION       = FIRST_SYSTEM_WINDOW+30;
	public static final int TYPE_VOICE_INTERACTION          = FIRST_SYSTEM_WINDOW+31;
	public static final int TYPE_ACCESSIBILITY_OVERLAY      = FIRST_SYSTEM_WINDOW+32;
	public static final int TYPE_VOICE_INTERACTION_STARTING = FIRST_SYSTEM_WINDOW+33;
	public static final int TYPE_DOCK_DIVIDER               = FIRST_SYSTEM_WINDOW+34;
	public static final int TYPE_QS_DIALOG                  = FIRST_SYSTEM_WINDOW+35;
	public static final int TYPE_SCREENSHOT                 = FIRST_SYSTEM_WINDOW + 36;
	public static final int TYPE_PRESENTATION               = FIRST_SYSTEM_WINDOW + 37;
	public static final int TYPE_APPLICATION_OVERLAY        = FIRST_SYSTEM_WINDOW + 38;
	public static final int LAST_SYSTEM_WINDOW              = 2999;


	public int flags;

	public static final int FLAG_ALLOW_LOCK_WHILE_SCREEN_ON     = 0x00000001;
	public static final int FLAG_DIM_BEHIND                     = 0x00000002;
	@Deprecated
	public static final int FLAG_BLUR_BEHIND                    = 0x00000004;
	public static final int FLAG_NOT_FOCUSABLE                  = 0x00000008;
	public static final int FLAG_NOT_TOUCHABLE                  = 0x00000010;
	public static final int FLAG_NOT_TOUCH_MODAL                = 0x00000020;
	@Deprecated
	public static final int FLAG_TOUCHABLE_WHEN_WAKING          = 0x00000040;
	public static final int FLAG_KEEP_SCREEN_ON                 = 0x00000080;
	public static final int FLAG_LAYOUT_IN_SCREEN               = 0x00000100;
	public static final int FLAG_LAYOUT_NO_LIMITS               = 0x00000200;
	public static final int FLAG_FULLSCREEN                     = 0x00000400;
	public static final int FLAG_FORCE_NOT_FULLSCREEN           = 0x00000800;
	@Deprecated
	public static final int FLAG_DITHER                         = 0x00001000;
	public static final int FLAG_SECURE                         = 0x00002000;
	public static final int FLAG_SCALED                         = 0x00004000;
	public static final int FLAG_IGNORE_CHEEK_PRESSES           = 0x00008000;
	public static final int FLAG_LAYOUT_INSET_DECOR 		    = 0x00010000;
	public static final int FLAG_ALT_FOCUSABLE_IM 			    = 0x00020000;
	public static final int FLAG_WATCH_OUTSIDE_TOUCH 			= 0x00040000;
	public static final int FLAG_SHOW_WHEN_LOCKED 			    = 0x00080000;
	public static final int FLAG_SHOW_WALLPAPER 				= 0x00100000;
	public static final int FLAG_TURN_SCREEN_ON 				= 0x00200000;
	@Deprecated
	public static final int FLAG_DISMISS_KEYGUARD 				= 0x00400000;
	public static final int FLAG_SPLIT_TOUCH 					= 0x00800000;
	public static final int FLAG_HARDWARE_ACCELERATED 			= 0x01000000;
	public static final int FLAG_LAYOUT_IN_OVERSCAN 			= 0x02000000;
	public static final int FLAG_TRANSLUCENT_STATUS 			= 0x04000000;
	public static final int FLAG_TRANSLUCENT_NAVIGATION 		= 0x08000000;
	public static final int FLAG_LOCAL_FOCUS_MODE 				= 0x10000000;
	public static final int FLAG_SLIPPERY 						= 0x20000000;
	public static final int FLAG_LAYOUT_ATTACHED_IN_DECOR 		= 0x40000000;
	public static final int FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS 	= 0x80000000;


WindowManager.LayoutParams的flags参数用于控制Window的显示特性

WindowManager.LayoutParams的type参数指定window的类型。
如果type参数表示一个系统Window，那么需要在AndroidManifest.xml中声明权限
`<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW">`，否则创建一个
系统Window时就会报错。

## 创建Window ##
创建一个Window，其实就是向这个Window中添加View视图。通过WindowManager提供的addView方法即可实现。
WindowManager继承自ViewManager接口，此接口提供了如下三个方法实现向一个Window中添加View视图，
或删除View视图，或改变View视图在Window中的显示特性

	public interface ViewManager{
	    public void addView(View view, ViewGroup.LayoutParams params);
	    public void updateViewLayout(View view, ViewGroup.LayoutParams params);
	    public void removeView(View view);
	}

