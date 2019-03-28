##View动画
1. **帧动画 FrameAnimation**
	> 图片一张接一张的播放
	
		1. 在res/drawable目录下定义动画文件animation.xml
			   <animation-list xmlns:android="http://schemas.android.com/apk/res/android"
				    android:oneshot="false"><!--false 表示播放多次-->
				    <!--200表示一张图片显示200ms,即每200ms切换一张图片-->
				    <item android:drawable="@drawable/logo1" android:duration="200" />
				    <item android:drawable="@drawable/logo2" android:duration="200" />
				    <item android:drawable="@drawable/logo3" android:duration="200" />
				</animation-list>
		
		2. 引用animation.xml实现帧动画
			   ImageView iv = (ImageView) findViewById(R.id.iv);
		       iv.setBackgroundResource(R.drawable.animation);
		       AnimationDrawable anim = (AnimationDrawable) iv.getBackground();
		       anim.start();
		       //anim.stop(); to stop animation

2. **补间动画 TweenAnimation**

	>a. **平移动画 TranslateAnimation**
	
		public void translate(View v) {

			// 定义一个平移动画
			/*
			 * TranslateAnimation animation = new TranslateAnimation( 0, 200, 0, 0);
			 */
	
			/*
			 * RELATIVE_TO_PARENT ：相对屏幕的宽高来说 倍数
			 * RELATIVE_TO_SELF ：相对自己控件的宽高来说 倍数
			 * Animation.ABSOLUTE ：绝对值 指的是像素
			 */
	
			TranslateAnimation animation = new TranslateAnimation(
					Animation.RELATIVE_TO_PARENT, 0,
				    Animation.RELATIVE_TO_PARENT, 0.5f,
				    Animation.RELATIVE_TO_PARENT, 0,
					Animation.RELATIVE_TO_PARENT, 0);
			// 定义动画时长
			animation.setDuration(2000);
			// 无限循环播放
			animation.setRepeatCount(Animation.INFINITE);//无限循环
	
			// 指定重复播放的模式
			//Animation.RESTART 当指定无限循环模式时，动画播放到最终位置，直接回到原位置，再从原位置重复播放动画
			//Animation.REVERSE 当指定无限循环模式时，动画播放到最终位置，以动画播放的形式回到原位置，再从原位置继续播放动画
			animation.setRepeatMode(Animation.RESTART);
	
			// 让图片播放这个动画
			//每次播放，2s时间内，View沿x轴正方向平移半个屏幕的距离
			iv.startAnimation(animation);
		}

		--------------------------------------------------------------------------------------
		
		使用xml文件定义平移动画（一般采用此方式）
		1. res/anim目录下定义translate.xml文件
			<?xml version="1.0" encoding="utf-8"?>
			<translate 
			      xmlns:android="http://schemas.android.com/apk/res/android"
			    android:fromXDelta="0" 
			    android:toXDelta="50%p"
			    android:duration="2000"
			    android:repeatCount="infinite"
			    android:repeatMode="reverse"
			  >
			</translate>

			note：   android:toXDelta="？"
						？中的值，可以写3中类型，如果直接写数字，代表的是绝对值的移动
						如果写50% ，代表的是自己的宽或高的1/2
						如果写50%p ,代表的是屏幕的1/2

		2. 引用translate.xml文件实现平移动画
			public void translate(View v) {
				Animation animation = AnimationUtils.loadAnimation(this, R.anim.translate);
				iv.startAnimation(animation);
			}
	
	>b. **旋转动画 RotateAnimation**

		public void rotate(View v) {

			RotateAnimation animation = new RotateAnimation(0,
					360,// 从什么角度旋转到什么角度
					Animation.RELATIVE_TO_SELF, 0.2f, 
					Animation.RELATIVE_TO_SELF, 0.5f);
	
			// 定义动画时长
			animation.setDuration(2000);
			// 无限循环播放
			animation.setRepeatCount(Animation.INFINITE);
	
			// 指定重复播放的模式
			animation.setRepeatMode(Animation.REVERSE);
	
			// 让图片播放这个动画
			//每次播放，在2s时间内，View以相对自身宽度0.2倍，自身高度0.5倍的坐标点为中心，从0度旋转到360度
			iv.startAnimation(animation);
		}

		--------------------------------------------------------------------------------------
		
		使用xml文件定义旋转动画（一般采用此方式）
		1. res/anim目录下定义rotate.xml文件
			<?xml version="1.0" encoding="utf-8"?>
			<rotate xmlns:android="http://schemas.android.com/apk/res/android"
			    android:fromDegrees="0" 
			    android:toDegrees="90" 
			    android:pivotX="50%"
			    android:pivotY="50%"
			    android:duration="2000" 
			    android:repeatCount="infinite" 
			    android:repeatMode="reverse">
			</rotate>

		2. 引用rotate.xml文件实现旋转动画
			public void rotate(View v) {
				Animation animation = AnimationUtils.loadAnimation(this, R.anim.rotete);
				iv.startAnimation(animation);
			}

	>c. **缩放动画 ScaleAnimation**

		public void scale(View v) {

			ScaleAnimation animation = new ScaleAnimation(3, 1, // x方向，从什么倍数到什么倍数
					3, 1,// y方向，从什么倍数到什么倍数
					Animation.RELATIVE_TO_SELF, 0.5f, // 放大的中心点 x方向
					Animation.RELATIVE_TO_SELF, 0.5f); // 放大的中心店 y方向
	
			// 定义动画时长
			animation.setDuration(2000);
			// 无限循环播放
			animation.setRepeatCount(Animation.INFINITE);
	
			// 指定重复播放的模式
			animation.setRepeatMode(Animation.REVERSE);
	
			// 让图片播放这个动画
			//每次播放，在2s时间内，View以相对自身宽度0.5倍，自身高度0.5倍的坐标点为中心，从原图的3倍大小缩放到原图的1倍大小
			iv.startAnimation(animation);
		}

		--------------------------------------------------------------------------------------
		
		使用xml文件定义旋转动画（一般采用此方式）
			类似平移，旋转动画的xml文件实现方式

	>d. **透明度渐变动画 AlphaAnimation**

		public void alpha(View v) {

			AlphaAnimation animation = new AlphaAnimation(0.5f, 1);
	
			// 定义动画时长
			animation.setDuration(2000);
			// 无限循环播放
			animation.setRepeatCount(Animation.INFINITE);
	
			// 指定重复播放的模式
			animation.setRepeatMode(Animation.REVERSE);
	
			// 让图片播放这个动画
			//每次播放，在2s时间内，View从透明度=0.5渐变到透明度=1
			iv.startAnimation(animation);
		}

		--------------------------------------------------------------------------------------
		
		使用xml文件定义旋转动画（一般采用此方式）
			类似平移，旋转动画的xml文件实现方式

	>e. **补间动画集合**

		public void set(View v){
			//包含 平移动画 、 旋转动画
			//Interpolator : 插入器   false : 每个动画使用自己的插入器，  true: 大家都使用集合里面的插入器，
			AnimationSet set = new AnimationSet(false);
			
			TranslateAnimation animation = new TranslateAnimation(
					Animation.RELATIVE_TO_PARENT, 0, 
					Animation.RELATIVE_TO_PARENT, 0.5f, 
					Animation.RELATIVE_TO_PARENT, 0,
					Animation.RELATIVE_TO_PARENT, 0.5f);
			animation.setDuration(2000);
			animation.setRepeatCount(TranslateAnimation.INFINITE);
			animation.setRepeatMode(TranslateAnimation.REVERSE);
			
			RotateAnimation ranimation = new RotateAnimation(0, 360,
					Animation.RELATIVE_TO_SELF, 0.5f, 
					Animation.RELATIVE_TO_SELF,0.5f);
			ranimation.setDuration(2000);
			ranimation.setRepeatCount(TranslateAnimation.INFINITE);
			ranimation.setRepeatMode(TranslateAnimation.REVERSE);
	
			ScaleAnimation sanimation = new ScaleAnimation(1, 2, // x方向，从什么倍数到什么倍数
					1, 2,// y方向，从什么倍数到什么倍数
					Animation.RELATIVE_TO_SELF, 0.5f, // 放大的中心点 x方向
					Animation.RELATIVE_TO_SELF, 0.5f); // 放大的中心店 y方向
			sanimation.setDuration(2000);
			sanimation.setRepeatCount(TranslateAnimation.INFINITE);
			sanimation.setRepeatMode(TranslateAnimation.REVERSE);
			
			AlphaAnimation aanimation = new AlphaAnimation(0, 1);
			aanimation.setDuration(2000);
			aanimation.setRepeatCount(TranslateAnimation.INFINITE);
			aanimation.setRepeatMode(TranslateAnimation.REVERSE);
			
			set.addAnimation(animation);
			set.addAnimation(ranimation);
			set.addAnimation(aanimation);
			
			imageView.startAnimation(set);
		}

		--------------------------------------------------------------------------------------
		
		使用xml文件定义旋转动画（一般采用此方式）
		1. res/anim目录下定义set.xml文件
			<?xml version="1.0" encoding="utf-8"?>
			<set>
			    <rotate
			        xmlns:android="http://schemas.android.com/apk/res/android"
			        android:duration="2000"
			        android:fromDegrees="0"
			        android:pivotX="50%"
			        android:pivotY="50%"
			        android:repeatCount="infinite"
			        android:repeatMode="reverse"
			        android:toDegrees="90" >
			    </rotate>
			
			    <translate
			        xmlns:android="http://schemas.android.com/apk/res/android"
			        android:duration="2000"
			        android:fromXDelta="0"
			        android:repeatCount="infinite"
			        android:repeatMode="reverse"
			        android:toXDelta="50%p" >
			    </translate>
			</set>

		2. 引用set.xml文件实现旋转动画
			public void set(View v) {
				Animation animation = AnimationUtils.loadAnimation(this, R.anim.set);
				iv.startAnimation(animation);
			}

	>**补间动画注意事项：**

	>补间动画的动画效果只是改变图片的位置，但ImageView的位置不会改变，
	>
	>即ImageView点击事件的作用区域不会随着图片动画的位置改变而改变

##属性动画 ObjectAnimator
1. Android3.0(API 11)之后才有

2. 属性动画会改变ImageView的位置，即ImageView点击事件的作用区域会随着图片动画的位置改变而改变

3. 属性动画中的动画属性是在View.java中定义的，如下：
	
			public static final Property<View, Float> ALPHA = new FloatProperty<View>("alpha")
			public static final Property<View, Float> TRANSLATION_X = new FloatProperty<View>("translationX")
			public static final Property<View, Float> TRANSLATION_Y = new FloatProperty<View>("translationY")
			public static final Property<View, Float> X = new FloatProperty<View>("x")
			public static final Property<View, Float> Y = new FloatProperty<View>("y")
	
			//以z轴为轴心旋转，z轴垂直于屏幕面
			public static final Property<View, Float> ROTATION = new FloatProperty<View>("rotation") 
	
			public static final Property<View, Float> ROTATION_X = new FloatProperty<View>("rotationX")
			public static final Property<View, Float> ROTATION_Y = new FloatProperty<View>("rotationY")
			public static final Property<View, Float> SCALE_X = new FloatProperty<View>("scaleX")
			public static final Property<View, Float> SCALE_Y = new FloatProperty<View>("scaleY")
		
4. 属性动画的使用

		可以通过调ObjectAnimator的如下方法实现
		public static ObjectAnimator ofFloat(Object target, //将要执行此属性动画的对象，一般为View控件对象
											 String propertyName,//上述c点中在View.java内定义的动画属性
											 float... values) //具体的属性动画需要传递的参数

		1. 平移动画
			public void translate(View v) {
				ObjectAnimator animator =  ObjectAnimator.ofFloat(
						imageView,
						"translationX",  //水平平移的动画
						new float[]{0 ,10,20,30, 100}); //从0位置，移动到100位置
					
				//new float[]{0, 10, 20, 30, 100}表示在动画时间内（此处为2s）会移动四段区域，
				//即0-10,10-20,20-30,30-100这四段区域，四段区域平分动画时间，此处即平移经过每段区域的时间都为500ms
				//所以平移动画效果在区域范围短的，较区域范围长的速度慢，产生一种变速运动的效果；
				
				//new floag[]{0, 100}表示在动画时间内，经过的区域只有0-100这一个，并且平移动画效果是匀速运动的。			

				animator.setDuration(2000);
				animator.setRepeatCount(ObjectAnimator.INFINITE);
				animator.setRepeatMode(ObjectAnimator.REVERSE);
				animator.start();
			}

			--------------------------------------------------------------------------------------
			使用xml文件定义平移动画
				1. res/animator目录下定义translate.xml文件
					<?xml version="1.0" encoding="utf-8"?>
					<objectAnimator xmlns:android="http://schemas.android.com/apk/res/android" 
					    android:propertyName="translationX"
					    android:valueFrom="0"
					    android:valueTo="150"
					    android:duration="2000"
					    android:repeatCount="infinite"
					    android:repeatMode="reverse"
					    >
					</objectAnimator>
	
				2. 引用translate.xml文件实现旋转动画
					public void translate(View v) {
						//从xml文件导入动画资源
						Animator anim = AnimatorInflater.loadAnimator(this, R.animator.traslate); 
						
						//指定哪一个控件去播放这个动作
						anim.setTarget(imageView);
						anim.start();
					}


		2. 旋转动画
			public void rotate(View v) {
				ObjectAnimator animator =  ObjectAnimator.ofFloat(
						imageView, 
						"rotationX",  //以x方向为中轴线旋转的动画
						new float[]{0 ,360}); //从0位置，旋转到100位置
				
				animator.setDuration(2000);
				animator.setRepeatCount(ObjectAnimator.INFINITE);
				animator.setRepeatMode(ObjectAnimator.REVERSE);
				animator.start();
			}

			--------------------------------------------------------------------------------------
			使用xml文件定义旋转动画，(参考平移动画的xml实现)

		3. 缩放动画
			public void scale(View v) {
				ObjectAnimator animator =  ObjectAnimator.ofFloat(
						imageView,
						"scaleX",  //只沿x轴进行缩放
						new float[]{1 ,3}); //图片在x轴方向上，从1倍大小缩放到3倍大小
				
				animator.setDuration(2000);
				animator.setRepeatCount(ObjectAnimator.INFINITE);
				animator.setRepeatMode(ObjectAnimator.REVERSE);
				animator.start();
			}

			--------------------------------------------------------------------------------------
			使用xml文件定义缩放动画，(参考平移动画的xml实现)

		4. 透明度渐变动画
			public void alpha(View v) {
				ObjectAnimator animator =  ObjectAnimator.ofFloat(
						imageView,
						"alpha",  //透明度动画
						new float[]{0 ,1}); //从完全透明到完全不透明
				
				animator.setDuration(2000);
				animator.setRepeatCount(ObjectAnimator.INFINITE);
				animator.setRepeatMode(ObjectAnimator.REVERSE);
				animator.start();
			}
		
			--------------------------------------------------------------------------------------
			使用xml文件定义透明度渐变动画，(参考平移动画的xml实现)

		5. 属性动画集合
			public void set(View v){
				//AnimationSet 补间动画的集合

				AnimatorSet set = new AnimatorSet();
				
				ObjectAnimator animator =  ObjectAnimator.ofFloat(
						imageView,
						"translationX",  //水平平移的动画
						new float[]{0 , 100}); //从0位置，移动到100位置
				
				animator.setDuration(2000);
				//animator.setRepeatCount(ObjectAnimator.INFINITE);
				//animator.setRepeatMode(ObjectAnimator.REVERSE);
				
				ObjectAnimator animator2 =  ObjectAnimator.ofFloat(
						iv,  //谁将要执行这个动画 一般放控件的对象
						"translationY",  //水平平移的动画
						new float[]{0 , 100}); //从0位置，移动到100位置
				
				animator2.setDuration(2000);
				//animator2.setRepeatCount(ObjectAnimator.INFINITE);
				//animator2.setRepeatMode(ObjectAnimator.REVERSE);
				
				//set.playTogether(animator , animator2); //同时播放两个动画

				//按顺序播放时如果animator设为无限重复播放模式，则animator2无法播放动画
				set.playSequentially(animator , animator2); //按顺序播放
				
				set.start(); //播放动画
			}

			--------------------------------------------------------------------------------------
			使用xml文件定义属性动画集合，(参考官方文档)

##View动画和属性动画的相关介绍可参考官方文档：
[https://developer.android.google.cn/guide/topics/resources/animation-resource](https://developer.android.google.cn/guide/topics/resources/animation-resource "Animation Resources")