## Build layouts for TV ##

### Use layout themes for TV ###
Android Themes can provide a basis for layouts in your TV apps. 
You should use a theme to modify the display of your app activities that are meant to run 
on a TV device.

**Leanback theme**
The v17 leanback support library includes Theme.Leanback, a theme for TV activities that 
provides a consistent visual style. 

	<activity
	  android:name="com.example.android.TvActivity"
	  android:label="@string/app_name"
	  android:theme="@style/Theme.Leanback">

The leanback theme does not include an action bar, since there none in an Android TV app.
If your app uses support fragments, your activity must extend FragmentActivity. 
Do not use AppCompatActivity which tries to theme the action bar and produces the error:

	java.lang.RuntimeException: Unable to start activity ComponentInfo{...} :
	java.lang.IllegalStateException: You need to use a Theme.AppCompat theme (or  
		descendant) with this activity.

**NoTitleBar theme**
The title bar is a standard user interface element for Android apps on phones and tablets,
but it is not appropriate for TV apps.

If you are not using v17 leanback classes, you should apply this theme to your TV
activities to suppress the display of a title bar. 

	<activity
		android:name="com.example.android.TvActivity"
		android:label="@string/app_name"
		android:theme="@android:style/Theme.NoTitleBar">
		...
	
	</activity>

### Build basic TV layouts ###
Layouts for TV devices should follow some basic guidelines to ensure they are usable and
effective on large screens. 

Follow these tips to build landscape layouts optimized for TV screens: 
1. Build layouts with a landscape orientation. TV screens always display in landscape mode.

2. Put on-screen navigation controls on the left or right side of the screen 
and save the vertical space for content.

3. Create UIs that are divided into sections, using Fragments, and use view groups like
GridView instead of ListView to make better use of the horizontal screen space. 

4. Use view groups such as RelativeLayout or LinearLayout to arrange views. 
This approach allows the system to adjust the position of the views to the size, alignment,
aspect ratio, and pixel density of a TV screen.

5. Add sufficient margins between layout controls to avoid a cluttered UI.

**Overscan**
Layouts for TV have some unique requirements due to the evolution of TV standards 
and the desire to always present a full screen picture to viewers. 
For this reason, TV devices may clip the outside edge of an app layout in order to ensure
that the entire display is filled. 
This behavior is generally referred to as overscan. 


Screen elements that must be visible to the user at all times should be positioned within
the overscan safe area. 
Adding a 5% margin of 48dp on the left and right edges and 27dp on the top and bottom edges
to a layout ensures that screen elements in that layout will be within the overscan safe 
area. 

Background screen elements that the user doesn't directly interact with should not be
adjusted or clipped to the overscan safe area. 
This approach ensures that background screen elements look correct on all screens. 

	<?xml version="1.0" encoding="utf-8"?>
	<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
	   android:layout_width="match_parent"
	   android:layout_height="match_parent"
	   >
	
	   <!-- Screen elements that can render outside the overscan safe area go here -->
	
	   <!-- Nested RelativeLayout with overscan-safe margin -->
	   <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
	       android:layout_width="match_parent"
	       android:layout_height="match_parent"
	       android:layout_marginTop="27dp"
	       android:layout_marginBottom="27dp"
	       android:layout_marginLeft="48dp"
	       android:layout_marginRight="48dp">
	
	      <!-- Screen elements that need to be within the overscan safe area go here -->
	
	   </RelativeLayout>
	</RelativeLayout>

Do not apply overscan margins to your layout if you are using the v17 leanback classes, 
such as BrowseFragment or related widgets, 
as those layouts already incorporate overscan-safe margins. 

### Build useable text and controls ###
The text and controls in a TV app layout should be easily visible and navigable from a 
distance.

Follow these tips to make your user interface elements easier to see from a distance: 
1. Break text into small chunks that users can quickly scan.
2. Use light text on a dark background. This style is easier to read on a TV.
3. Avoid lightweight fonts or fonts that have both very narrow and very broad strokes. 
Use simple sans-serif fonts and anti-aliasing to increase readability.

4. Use Android's standard font sizes: 

		<TextView
	      android:id="@+id/atext"
	      android:layout_width="wrap_content"
	      android:layout_height="wrap_content"
	      android:gravity="center_vertical"
	      android:singleLine="true"
	      android:textAppearance="?android:attr/textAppearanceMedium"/>

5. Ensure that all your view widgets are large enough to be clearly visible to someone
sitting 10 feet away from the screen (this distance is greater for very large screens).
The best way to do this is to use layout-relative sizing rather than absolute sizing, 
and density-independent pixel (dip) units instead of absolute pixel units.
For example, to set the width of a widget, use wrap_content instead of a pixel measurement,
and to set the margin for a widget, use dip values instead of px values.

### Manage layout resources for TV ###
The common high-definition TV display resolutions are 720p, 1080i, and 1080p.
Your TV layout should target a screen size of 1920 x 1080 pixels, 
and then allow the Android system to downscale your layout elements to 720p if necessary.

In general, downscaling (removing pixels) does not degrade your layout presentation
quality.
However, upscaling can cause display artifacts that degrade the quality of your layout
and have a negative impact on the user experience of your app.

To get the best scaling results for images, provide them as 9-patch image elements 
if possible. 

If you provide low quality or small images in your layouts, they will appear pixelated, 
fuzzy, or grainy, which is not a good experience for the user. Use high-quality images 
instead. 


### Avoid layout anti-patterns ###
There are a few approaches to building layouts that you should avoid because they do not
work well on TV devices and lead to bad user experiences. 

Here are some user interface approaches you should specifically not use 
when developing a layout for TV. 
1. Re-using phone or tablet layouts - Do not reuse layouts from a phone or tablet app 
without modification.
Layouts built for other Android device form factors are not well suited for TV devices 
and should be simplified for operation on a TV.

2. ActionBar - While this user interface convention is recommended for use on phones and
tablets, it is not appropriate for a TV interface.
In particular, using an action bar options menu (or any pull-down menu for that matter) is
strongly discouraged, due to the difficulty in navigating such a menu with a remote 
control.

3. ViewPager - Sliding between screens can work great on a phone or tablet, but don't try 
this on a TV!


### Handle large bitmaps ###
TV devices, like any other Android device, have a limited amount of memory. 
If you build your app layout with very high-resolution images or use many high-resolution 
images in the operation of your app, it can quickly run into memory limits 
and cause out of memory errors. 

For most cases, we recommend that you use the **Glide** library to fetch, decode, 
and display bitmaps in your app. 

For more information on getting the best performance when working with bitmaps, 
refer to our general **Android graphics best practices**. 