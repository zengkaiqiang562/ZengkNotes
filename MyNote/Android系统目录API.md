Context.getApplicationInfo().dataDir

	/data/user/0/com.minxing.client  //此目录会被链接到 /data/data/com.minxing.client 下


		
Environment.getExternalStorageDirectory().getAbsolutePath();

获取路径：/storage/emulated/0



Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DCIM).getAbsolutePath()

获取路径：/storage/emulated/0/DCIM