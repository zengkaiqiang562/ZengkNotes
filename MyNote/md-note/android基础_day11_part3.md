##拍照
1. 官网的GUIDES部分的Camera有介绍

2. 简单代码示例：
	>需要权限
	>
	>`<uses-permission android:name="android.permission.CAMERA" />`
	>
    >`<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />`
    >
    >`<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>`

		public void click(View v){
		    // create Intent to take a picture and return control to the calling application
		    Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
	
		    file = new File("/mnt/sdcard/"+System.currentTimeMillis()+".jpg");
		    Uri fileUri = Uri.fromFile(file);
		    
		    //这个传递的数值代表的意思是：告诉系统照相机，拍完照片之后，把那张照片保存到给你的uri路径下
		    intent.putExtra(MediaStore.EXTRA_OUTPUT, fileUri); // set the image file name
	
		    // start the image capture Intent
		    startActivityForResult(intent, 1);
		}
		
		@Override
		protected void onActivityResult(int requestCode, int resultCode, Intent data) {
			//转化图片
			if(file != null){
				Bitmap bitmap = BitmapFactory.decodeFile(file.getAbsolutePath());
				iv.setImageBitmap(bitmap);
			}
		}

##录制视频
1. 官网的GUIDES部分的Camera有介绍

2. 简单代码示例
	>需要的权限同拍照中介绍

		public void click(View v){
		    Intent intent = new Intent(MediaStore.ACTION_VIDEO_CAPTURE);
		    
		    file = new File("/mnt/sdcard/"+System.currentTimeMillis()+".3gp");//视频格式文件
		    Uri fileUri = Uri.fromFile(file);
		    
		    intent.putExtra(MediaStore.EXTRA_OUTPUT, fileUri);
		    
		    intent.putExtra(MediaStore.EXTRA_VIDEO_QUALITY, 1); // set the video image quality to high
	
		    startActivityForResult(intent, 1);
		}
		
		@Override
		protected void onActivityResult(int requestCode, int resultCode, Intent data) {
			//转化图片
			if(file != null){
				vv.setVideoPath(file.getAbsolutePath());
				
				MediaController controller = new MediaController(this);
				controller.setAnchorView(vv);
				
				vv.setMediaController(controller);
				
				vv.start();
			}
		}


##传感器
1. 传感器类型介绍  
	>`#define SENSOR_TYPE_ACCELEROMETER                  1 //加速度`
	>
	>`#define SENSOR_TYPE_MAGNETIC_FIELD                 2 //磁力`
	>
	>`#define SENSOR_TYPE_ORIENTATION                    3 //方向`
	>
	>`#define SENSOR_TYPE_GYROSCOPE                      4 //陀螺仪`
	>
	>`#define SENSOR_TYPE_LIGHT                          5 //光线感应`
	>
	>`#define SENSOR_TYPE_PRESSURE                       6 //压力`
	>
	>`#define SENSOR_TYPE_TEMPERATURE                    7 //温度 `
	>
	>`#define SENSOR_TYPE_PROXIMITY                      8 //接近`
	>
	>`#define SENSOR_TYPE_GRAVITY                        9 //重力`
	>
	>`#define SENSOR_TYPE_LINEAR_ACCELERATION            10//线性加速度`

2. 获取手机上的所有传感器

		//1.得到传感器管理者
		SensorManager manager = (SensorManager) getSystemService(SENSOR_SERVICE);
		
		//2.获取手机上的所有传感器
		List<Sensor> sensors = manager.getSensorList(Sensor.TYPE_ALL);
		StringBuilder sb = new StringBuilder();
		for(Sensor s : sensors){
			String name = s.getName();
			int type = s.getType();
			sb.append("name : "+name + " -- type : + type + "\r\n");
		}
		tv.setText(sb.toString());

3. 光线传感器

		SensorManager manager = (SensorManager) getSystemService(SENSOR_SERVICE);
		
		//2.得到光线传感器对象
		Sensor sensor = manager.getDefaultSensor(Sensor.TYPE_LIGHT);
		
		//3.监听一个传感器的数据。
		manager.registerListener(new SensorEventListener() {
			
			//数据发生了改变
			@Override
			public void onSensorChanged(SensorEvent event) {
				float value = event.values[0]; //得到当前的亮度值
				tv.setText("当前的亮度是---》："+value);
			}
			
			//精度发生了改变
			@Override
			public void onAccuracyChanged(Sensor sensor, int accuracy) {
				
			}
		}, sensor,  SensorManager.SENSOR_DELAY_NORMAL);

4. 方向传感器（指南针）

		SensorManager manager = (SensorManager) getSystemService(SENSOR_SERVICE);

		Sensor sensor = manager.getDefaultSensor(3);//Sensor.TYPE_ORIENTATION

		manager.registerListener(new SensorEventListener() {

			@Override
			public void onSensorChanged(SensorEvent event) {
				// values[0]: 手机的Y轴与正北方向的夹角 . 0=North, 90=East, 180=South,
				// 270=West
				int value = (int) event.values[0];
				String str = "";

				if (value == 0) {
					str = "正北方向：" + value;
				} else if (value == 90) {
					str = "正东方向：" + value;
				} else if (value == 180) {
					str = "正南方向：" + value;
				} else if (value == 270) {
					str = "正西方向：" + value;
				} else if (value > 0 && value < 90) {

					str = "东北方向：" + value;
				} else if (value > 90 && value < 180) {

					str = "东南方向：" + value;
				} else if (value > 180 && value < 270) {

					str = "西南方向：" + value;
				} else if (value > 270 && value < 360) {
					str = "西北方向：" + value;
				}
				
				tv.setText(str);
			}

			@Override
			public void onAccuracyChanged(Sensor sensor, int accuracy) {

			}
		}, sensor, SensorManager.SENSOR_DELAY_NORMAL);