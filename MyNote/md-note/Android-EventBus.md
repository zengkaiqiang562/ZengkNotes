## EventBus 介绍 ##
EventBus即发布——订阅事件总线。
EventBus简化了应用程序内各组件之间，组件于后台线程之间的通信。
其特点是开销小，代码更优雅，以及将发布者和订阅者解耦。

**EventBus的三要素**
1. Event（事件）：可以是任意类型的对象。

2. Subscriber（事件订阅者）： 事件订阅者指的是实现了事件处理方法（订阅者方法）的类。
在EventBus3.0 之前，事件处理方法只能限定于onEvent、onEventMainThread、onEventBackgroundThread
和onEventAysnc这四种，它们分别代表4种线程模型。
在EventBus3.0 之后，事件处理方法可以随便取名，但是需要添加一个注解 @Subscribe，并且要指定线程
模型。

3. Publisher（事件发布者）： 可以在任意线程任意位置发送事件，调用EventBus的post(Object)方法
即可发布事件。

**EventBus的四种ThreadMode（线程模型）**
1. POSTING：这是默认的线程模型。如果事件处理方法指定为POSTING线程模型，那么该事件是在哪个线程中
发布出来的，这个事件处理方法就会在哪个线程中执行。即发布事件和处理事件在同一个线程中实现。在此线程模型
下，需尽量避免事件处理方法中执行耗时操作，因为它会阻塞事件的传递，甚至引起ANR。

2. MAIN： 事件处理方法会在UI线程中执行。此时，事件处理方法同样不能执行耗时操作。

3. BACKGROUND： 如果事件在UI线程中发布，那么该事件的处理方法会在新的线程中执行；如果事件在子线程中
发布，那么该事件的处理方法就在发布事件的子线程中执行。此线程模式下，禁止事件处理方法中进行对UI的操作。

4. ASYNC： 无论事件在哪个线程中发布，该事件的处理方法都会在新建的子线程中执行。

## 使用EventBus ##
在项目中使用EventBus之前，需要在build.gradle中添加依赖库：

	implementation 'org.greenrobot:eventbus:3.0.0'

使用EventBus的基本步骤：
step1. 定义一个代表事件的Java类

	public class MessageEvent {
	 
	    public final String message;
	 
	    public MessageEvent(String message) {
	        this.message = message;
	    }
	}

step2. 在表示事件订阅者的类中实现事件处理方法（订阅者方法）。
在一个类中，调用EventBus的register方法将此类注册为事件订阅者，并在此类中实现订阅者方法。
在Android中，当把Activity或Fragment注册为订阅者时，register方法或unregister方法应该在生命周期方法
中执行。
只有调用register注册为事件订阅者之后，才能收到EventBus发布的事件，在执行完register方法之前发布的事件
无法接收到。

	@Override
	public void onStart() {
	    super.onStart();
	    EventBus.getDefault().register(this);
	}
	 
	@Override
	public void onStop() {
	    EventBus.getDefault().unregister(this);
	    super.onStop();
	}

	@Subscribe(threadMode = ThreadMode.MAIN)  //如果没有声明threadMode的类型，默认使用POSTING
	public void onMessageEvent(MessageEvent event) {
	    Toast.makeText(getActivity(), event.message, Toast.LENGTH_SHORT).show();
	}

step3. 发布事件

	EventBus.getDefault().post(new MessageEvent("Hello everyone!"));

可以在任意代码处调用post方法发布事件，只要是在调用此post方法之前注册了的订阅者中，实现的订阅者方法
处理的事件类型跟此post方法发布的事件类型相匹配，那么注册了的订阅者就会收到此事件，并执行事件类型匹配
的订阅者方法。

## 代码混淆的规则 ##
因为订阅者方法不是被直接调用的，所以当启动代码混淆时，ProGuard混淆机制会认为订阅者方法没有使用，
可能会移除订阅者方法。所以要在混淆文件中阻止订阅者方法被移除，混淆文件的配置方式如下：

	-keepattributes *Annotation*
	-keepclassmembers class * {
	    @org.greenrobot.eventbus.Subscribe <methods>;
	}
	-keep enum org.greenrobot.eventbus.ThreadMode { *; }
	 
	# Only required if you use AsyncExecutor
	-keepclassmembers class * extends org.greenrobot.eventbus.util.ThrowableFailureEvent {
	    <init>(java.lang.Throwable);
	}

## 发布黏性事件（sticky event） ##
当一个事件对象被发布后，还需要保留时，可使用postSticky(Object)的方式发布出去。
当已经发布了一个黏性事件后，如果后续注册的订阅者中实现了一个处理此黏性的事件类型的订阅者方法，
那么这个后注册的订阅者还是能接收到这个已经发布了的黏性事件。

	EventBus.getDefault().postSticky(new MessageEvent("Hello everyone!"));	
	
	---------------------

	@Override
	public void onStart() {
	    super.onStart();
	    EventBus.getDefault().register(this);
	}
	
	// 订阅黏性事件时要设置sticky属性为true
	@Subscribe(sticky = true, threadMode = ThreadMode.MAIN)
	public void onEvent(MessageEvent event) {   
	    textField.setText(event.message);
	}
	
	@Override
	public void onStop() {
	    EventBus.getDefault().unregister(this);    
	    super.onStop();
	}

一个最近发布的黏性事件，除了能被后续注册的匹配此黏性事件类型的订阅者接收外，还能调用EventBus提供的API
主动获取这个最近发布的黏性事件对象，另外还能删除最近发布的黏性事件：

	MessageEvent stickyEvent = EventBus.getDefault().getStickyEvent(MessageEvent.class);
	// Better check that an event was actually posted before
	if(stickyEvent != null) {
	    // "Consume" the sticky event
	    EventBus.getDefault().removeStickyEvent(stickyEvent);
	    // Now do something with it
	}