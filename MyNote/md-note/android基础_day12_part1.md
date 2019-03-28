##数据库事务
> 数据库事务(Database Transaction) ，是指作为单个逻辑工作单元执行的一系列操作，要么完全地执行，要么完全地不执行。 
> 
> 事务处理可以确保除非事务性单元内的所有操作都成功完成，否则不会永久更新面向数据的资源。
> 
> 通过将一组相关操作组合为一个要么全部成功要么全部失败的单元，可以简化错误恢复并使应用程序更加可靠。
##Fragment
1. **概述**
	> a. 使用Fragment实现对界面的局部更新，
	> 
	> b. Fragment不需要在AndroidManifest.xml中注册
	>
	> c. Fragment在androi3.0后才出现的，android4.0谷歌提供出API
	>
	> d. Fragment通过事务处理的方式嵌入到Activity界面中，保证Fragment要么不嵌入到界面中，要么能完整的嵌入到界面中。避免出现Fragment嵌入时发生错误而导致的花屏等现象


2. **简单的使用方式**

	>a. 创建Fragment相关类

		import android.app.Fragment;
		import android.os.Bundle;
		import android.view.LayoutInflater;
		import android.view.View;
		import android.view.ViewGroup;
		
		/**
		 * 这是显示相关的碎片，一会要显示到容器里面去
		 *
		 */
		public class ShowFragment extends Fragment{
		
			//用来控制当前的碎片显示生命内容，有点类似activity里面onCreate方法
			@Override
			public View onCreateView(LayoutInflater inflater, ViewGroup container,
					Bundle savedInstanceState) {
				//View.inflate(context, resource, root)
				//返回一个view对象，实际上指，让当前的碎片，显示这个布局
				return inflater.inflate(R.layout.show, null);
			}
		}

	>b. 将创建的Fragment嵌入到Activity中

		import android.app.Activity;
		import android.app.FragmentManager;
		import android.app.FragmentTransaction;
		import android.os.Bundle;
		import android.view.View;
		
		public class MainActivity extends Activity {
			FragmentManager manager;
		
			FragmentTransaction tr;
		
			@Override
			protected void onCreate(Bundle savedInstanceState) {
				super.onCreate(savedInstanceState);
				setContentView(R.layout.activity_main);
		
				// 1.得到fragment的管理者
				manager = getFragmentManager();
		
				// 2.打开事务 事务只能打开一次，提交一次，不能重复提交
				tr = manager.beginTransaction();
		
				ShowFragment fragment = new ShowFragment();
		
				// 3.让指定的fragment显示到指定的容器里面去。
				tr.replace(R.id.container, fragment);
		
				// 4.提交事务
				tr.commit();
			}

			...
		}


3. Fragment向下兼容
	> a. 因为Fragment是Android3.0之后才有的，所以对于Android2.0等低版本没有Fragment的API。此时则需要使用v4包中的Fragment来做兼容
	> 
	> b. 兼容方式：
	>
		 ①fragment修改导包，导android.support.v4.app.fragment
		 ②在activity里面也要继承FragmentActivity
		 ③getFragmentManager修改成getSupportFragmentManager