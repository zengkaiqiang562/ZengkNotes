TV apps use the same structure as those for phones and tablets. 
This similarity means you can modify your existing apps to also run on TV devices
 or create new apps based on what you already know about building apps for Android. 


These are the main components you should use to create an app that runs on TV devices: 
- Activity for TV (Required) - In your application manifest, 
declare an activity that is intended to run on TV devices.

- TV Support Libraries (Optional) - There are several support libraries available for 
TV devices that provide widgets for building user interfaces.


### Declare a TV activity ###
An application intended to run on TV devices must declare a launcher activity for TV
in its manifest.
It uses a CATEGORY_LEANBACK_LAUNCHER intent filter to do this. 
This filter identifies your app as being enabled for TV, and lets Google Play 
identify it as a TV app.

When a user selects your app on their TV home screen, this intent identifies which
activity to launch.

	<activity
	    android:name="com.example.android.MainActivity"
	    android:label="@string/app_name" >
	
	    <intent-filter>
	      <action android:name="android.intent.action.MAIN" />
	      <category android:name="android.intent.category.LAUNCHER" />
	    </intent-filter>
	  </activity>
	
	  <activity
	    android:name="com.example.android.TvActivity"
	    android:label="@string/app_name"
	    android:theme="@style/Theme.Leanback">
	
	    <intent-filter>
	      <action android:name="android.intent.action.MAIN" />
	      <category android:name="android.intent.category.LEANBACK_LAUNCHER" />
	    </intent-filter>
	
	  </activity>

The second activity manifest entry in this example specifies that 
activity as the one to launch on a TV device. 

If you do not include the CATEGORY_LEANBACK_LAUNCHER intent filter in your app, it is not
visible to users running Google Play on TV devices
if your app does not have this filter when you use developer tools to load it onto a 
TV device, the app does not appear in the TV user interface.


**Activity layout **
If you are modifying an existing app for use on TV, your app should not use the same 
activity layout for TV that it does for phones and tablets。
Your TV app's user interface (or TV portion of your existing app) should provide a simpler
interface for easy navigation using a remote control from a couch.


### Declare Leanback support ###
Declare that your app uses the Leanback user interface required by Android TV.

	<manifest>
	    <uses-feature android:name="android.software.leanback"
	        android:required="false" />
	    ...
	</manifest>

- If you are developing an app that runs on mobile (phones, wearables, tablets, etc.) as
well as Android TV, set the required attribute value to false

- If you set the required attribute value to true, your app will run only on devices that 
use the Leanback UI. 

### Declare touchscreen not required ###
Applications that are intended to run on TV devices do not rely on touch screens for input.
To make this clear, your TV app's manifest must declare that the 
android.hardware.touchscreen feature is not required.  

	<manifest>
	    <uses-feature android:name="android.hardware.touchscreen"
	              android:required="false" />
	    ...
	</manifest>

This setting identifies your app as being able to work on a TV device,
and is required for your app to be considered a TV app in Google Play.

### Provide a home screen banner ###
If an application includes a Leanback launcher intent filter, 
it must provide a home screen banner image for each localization.

The banner is the app launch point that appears on the home screen in the apps and games 
rows.

	<application
	    ...
	    android:banner="@drawable/banner" >
	
	    ...
	</application>

Use the android:banner attribute with the <application> tag to supply a default banner 
for all application activities, 
or with the <activity> tag to supply a banner for a specific activity. 

The banner should be an xhdpi resource with a size of 320 x 180 px.

### Change the launcher color ###
When a TV app launches, the system displays an animation that resembles an expanding, 
filled circle. 

To customize the color of this animation, set the android:colorPrimary attribute of
your TV app or activity to a specific color.
You should also set two additional transition overlap attributes to true

	<resources>
	    <style ... >
	      <item name="android:colorPrimary">@color/primary</item>
	      <item name="android:windowAllowReturnTransitionOverlap">true</item>
	      <item name="android:windowAllowEnterTransitionOverlap">true</item>
	    </style>
	</resources>

### Add TV support libraries ###
The Android SDK includes support libraries for use with TV apps.
The libraries, which are located in the <sdk>/extras/android/support/ directory, 
are as follows: 
1.  v17 leanback library - Provides user interface widgets for TV apps, 
particularly for apps that do media playback. 

2.  v7 recyclerview library - Provides classes for managing the display of long lists in a
memory-efficient manner. 

3.  v7 cardview library - Provides user interface widgets for displaying information cards
such as media item pictures and descriptions. 

> You are not required to use these support libraries for your TV app. 
> However, we strongly recommend using them, particularly for apps that 
> provide a media catalog browsing interface. 

If you decide to use the v17 leanback library for your app, 
you should note that it is dependent on the v4 support library. 








 





