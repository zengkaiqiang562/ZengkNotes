##getX和 getRawX的区别

* getX

> 当前点击的坐标位置与控件的左边距离是多少

* getRawX

> 当前点击的坐标位置与屏幕的左边距离是多少

##bitmap.setPixel(int x, int y, int color);
>设置bitmap在坐标为(x,y)的像素点上的颜色为color，要想设置bitmap在某一范围内的颜色，如下所示：

		//将以xPoint,yPonit为圆心，半径为4的圆形范围内的像素点的颜色设为透明
		for (int i = -4; i < 5; i++) {
			for (int j = -4; j < 5; j++) {
				//为了要实现出来触摸显示一个圆形，必须对当前的i 和 j进行判定
				if(Math.sqrt(i*i +j*j) <= 4 ){
					//让指定的坐标点的颜色变成透明的
					copyBitmap.setPixel(xPoint+i, yPoint+j, Color.TRANSPARENT);
				}
			}
		}


##获取图片EXIF信息（图片格式，大小，尺寸，生成日期等）

		try {
			//获取到文件里面保存的头额外信息
			ExifInterface exif = new ExifInterface("/mnt/sdcard/big.jpg");
			String imgHeight =  exif.getAttribute(ExifInterface.TAG_IMAGE_LENGTH) ; //获取到图片的高度
			String imgWidth = exif.getAttribute(ExifInterface.TAG_IMAGE_WIDTH) ; //获取到图片的宽度 
			//TODO others
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

##assets文件夹和res/raw文件夹
1. res/raw和assets的相同点：
	>a. 两者目录下的文件在打包后会原封不动的保存在apk包中，不会被编译成二进制。

	>b. assets和res/raw工程目录下都可以放一些小于1M的文件（2.3版本以前要求，否则将不能读出数据。）,这些文件将被打包到APK中应用使用。
	>
	>c. assets目录下的文件将不做任何处理被打包，如果你的资源很大，例如视频等等，应该单独存储在文件系统中。
	
2. res/raw和assets的不同点：
	>a. res/raw中的文件会被映射到R.java文件中，访问的时候直接使用资源ID即R.raw.filename；assets文件夹下的文件不会被映射到
	R.java中，访问的时候需要AssetManager类。
	>
	>b. res/raw不可以有目录结构，而assets则可以有目录结构，也就是assets目录下可以再建立文件夹

3. 读取文件资源方法：
	>1.读取res/raw下的文件资源，通过以下方式获取输入流来进行写操作
	
		InputStream is = getResources().openRawResource(R.raw.filename);
		  
	>2.读取assets下的文件资源，通过以下方式获取输入流来进行写操作

		AssetManager am = null;  
		am = getAssets();  
    	InputStream is = am.open("filename"); 

	>3.通过URI访问raw资源

		Uri uri = Uri.parse("android.resource://package_name/raw/sample.png");


##内存溢出和内存泄露
1. 内存溢出

	> 内存不够用了，需要10M，但是只有2M

2. 内存泄露

	> 一个对象已经没有人使用了，但是还有引用指向，占据着内存，没有释放。