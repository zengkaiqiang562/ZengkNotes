# Android中IPC相关知识点介绍 #
1. IPC即Inter-Process Communication的缩写，译为进程间通信或跨进程通信。是指两个进程之间进行数据交换的过程。

2. 进程和线程
	- 线程： CPU调度的最小单元，同时线程是一种有限的系统资源。
	- 进程： 一般是指一个执行单元，在PC和移动设备上指一个程序或者一个应用。
	- 进程和线程是包含和被包含的关系，一个进程可以包含多个线程。一个进程中也可以只有一个线程，即主线程。Android中主线程也叫UI线程。

3. 当一个进程中需要执行大量耗时的任务时，如果这些任务都放在主线程中执行，就会造成界面无响应，这种情况在PC系统和移动系统中都存在，在Android中也称之为ANR（Application Not Responding），即应用无响应。解决这个问题就需要用到子线程，把一些耗时的任务另外放在子线程中执行即可。

4. 任何一个操作系统都需要有相应的IPC机制
	- Windows： 通过剪贴板、管道、邮槽等来进行进程间通信；
	- Linux： 通过命名管道、共享内存、信号量来进行进程间通信；
	- Android： 虽然Android是基于Linux内核的移动操作系统，但它的进程间通信方式并没有完全继承Linux。Android有自己的进程间通信方式。如Binder、Socket。Binder是Android中最有特色的进程间通信方式，通过Binder可以轻松地实现进程间通信。Socket既可以实现一个设备上的两个进程间的通信，也可以实现任意两个设备终端之间的通信。

5. 出现多进程的两种情况
	1. 应用因为某些原因，自身需要采用多进程模式来实现。原因如应用中有些特殊模块需要运行在单独的进程中，又如为了加大一个应用可使用的内存从而通过多进程来获取多份内存空间。
	2. 当前应用需要跟其他应用进行数据交互。由于是两个不同的应用，所以必须采用跨进程的方式来传递数据。比如系统提供的ContentProvider去查询数据时，其实就是一种进程间通信，只不过通信细节被系统内部屏蔽了而已。 

# Android中的多进程模式介绍 #
正常情况下，Android中多进程是指一个应用中存在多个进程的情况，因此下述所指的多进程模式只是针对一个应用中存在多个进程的情况，不讨论两个应用之间的多进程情况。

## 开启多进程模式的方式 ##
1. 在一个应用中开启多进程常用的方式就是给四大组件（Activity、Service、BroadcastReceiver、ContentProvider）在AndroidManifest.xml中指定android:process属性。
	- 如果没有为四大组件指定android:process属性，则默认进程的进程名就是包名。如果指定了process属性，则开启一个进程运行组件，进程名由process属性值指定。
	- 私有进程： process属性值以":"号开头的组件运行在当前应用的私有进程中。其他应用的组件不可以和当前应用的私有进程中的组件跑在同一个进程中。私有进程名以 当前应用包名 + process属性值 组成。
	- 全局进程： process属性值不以":"号开头的组件运行在全局进程中。其他应用的组件可以通过ShareUID的方式和当前应用的全局进程跑在同一个进程中。全局进程名即是process属性值。

	> Android系统会为每个应用分配一个唯一的UID，具有相同UID的应用才能共享数据。要说明的是，两个应用通过ShareUID跑在同一个进程中是有要求的，需要这两个应用有相同的ShareUID并且签名相同才可以。
	> 当两个应用有相同的ShareUID并且签名相同时，即使它们没有跑在同一个进程中，它们之间也可以互相访问对方的私有数据，如data目录，组件信息等。如果它们还跑在同一个进程中，那么除了能共享data目录，组件信息。还可以共享内存数据，看起来就像是一个应用的两个部分。 

2. 另外还有一种非常规的方式，即通过JNI在native层fork一个新的进程。

## 多进程模式存在的问题 ##
Android会为每个应用分配一个独立的虚拟机，实际上是为每个进程分配一个独立的虚拟机，不同的虚拟机在内存分配上有不同的地址空间，这就导致在不同的虚拟机中访问同一个类的对象会产生多份副本。
当通过android:process属性在一个应用中开启多进程时，对于运行在不同进程中的四大组件，如果它们之间需要通过内存来共享数据，在没有中间层介入的情况下，都会共享失败，因为两个不同的进程使用的内存空间不同，所以操作的内存数据是不一样的。这是多进程所带来的主要问题。

多进程存在的问题有如下几方面：
1. 静态成员和单例模式完全失效
	- 不同进程使用不同的内存空间，静态成员在每个进程中都有一个单独的副本。
2. 线程同步机制完全失效
	- 因为内存空间是分开的，所以不同进程的锁对象其实不是同一个，因此无法保证线程同步。
3. SharedPreferences的可靠性下降
	- SharedPreferences不支持两个进程同时去执行写操作，否则会导致一定几率的数据丢失。
4. Application会多次创建
	- 由于系统在创建新进程的同时会分配独立的虚拟机，这个过程其实就是一个启动应用的过程。所以当一个组件需要运行在一个新进程中时，系统相当于对这个应用重新启动了一次。而启动应用就会创建新的Application。

综上，多进程模式存在这些问题的原因，就是不同进程的组件会拥有独立的虚拟机、Application对象、以及不同的内存空间。
一个应用中的多进程情况，就相当于两个不同的应用采用了SharedUID的模式。

如果我们需要实现不同进程间的组件共享内存数据，需要通过跨进程通信的方式（IPC方式）间接地实现内存数据的共享。如使用Intent传递数据，基于Binder的Messenger和AIDL，以及Socket等。

# IPC基础概念介绍 ##

## 对象序列化 ##
对象序列化机制就是把内存中的Java对象转换成二进制流，并且能将这种二进制流保存在磁盘上，或者在网络中
传输这种二进制流对象。并且一旦其他程序获取了这种二进制流，都可以将它恢复成原来的Java对象

对象序列化机制包含了两方面的内容：对象的序列化（Serialize）和对象的反序列化（Deserialize）
- 对象的序列化是指将Java对象写入IO流
- 对象的反序列化是指从IO流中恢复Java对象

**Java类的对象支持序列化机制的条件**
只有可序列化的类，它的对象才能支持序列化机制。要想让一个类成为可序列化的类，则它必须实现如下
两个接口之一：Serializable、Externalizable。

在Android中，实现了Parcelable接口的类也是可序列化的。

### 使用 Serializable 接口实现对象序列化 ###
Serializable接口是一个标记接口，即该接口没有提供任何需要实现类去重写的方法，它只是表明一个实现类的实例
对象是可序列化的。

**对象序列化时，要注意一下几点：**

- 对象的类名，实例变量（即非静态的成员变量，包括基本类型，数组，对其他对象的引用类型的变量）
都会被序列化；方法，类变量（static修饰的静态成员变量）、transient实例变量（即transient修饰的
实例变量，也被称为瞬态实例变量），都不会被序列化；

- 当一个可序列化的类有多个父类时（包括直接父类和间接父类），这些父类要么也是可序列化的，要么就提供一个
无参的构造方法。当父类是可序列化的类时，那么父类的实例变量（包括私有的）也会被序列化；
当父类是不可序列化的类时，父类中的实例变量不会被序列化，但在反序列操作时，要求父类提供一个无参构造方法，
否则会报InvalidClassException异常

- 可序列化类（实现了Serializable接口）中，如果需要让某个实例变量不被序列化，则应该用transient关键字
修饰该实例变量。虽然static关键字也能达到这种效果，但不能这样使用static关键字。

- 因为可序列化中的实例变量也会被序列化，所以必须保证实例变量是可序列化的（即对于引用类型的实例变量，
其引用类型中的类也应该实现Serializable接口），否则应该使用transient关键字修饰这个实例变量，不让它
参与序列化，因为一旦让不能序列化的实例变量参与了序列化，则会报NotSerializableException异常

- 反序列化对象时必须要有序列化对象的class文件，否则会报ClassNotFoundException异常

- 当通过ObjectInputStream对来自文件或网络的IO流形式的序列化对象进行反序列化时，调用readXxx方法的
读取顺序要跟之前调用ObjectOutputStream.writeXxx对可序列化对象进行序列化操作的顺序一样。

- 反序列化机制不需要通过构造器来初始化Java对象

- 对一个可序列化对象obj进行序列化和反序列化操作后，再次获得的对象obj'，跟之前的对象obj不是同一个对象，
并且obj'对象中引用类型的实例变量所引用的对象跟之前的对象obj中的引用类型的实例变量所引用的对象也不是
同一个对象。

- transient关键字只能用于修饰实例变量，表示该实例变量不参与序列化过程。不可用transient去修饰其他
Java程序中的成分。

- 可序列化对象中被transient修饰的实例变量是不参与序列化过程的。所以，在反序列化时，恢复这个对象无法
获取到被transient修饰的实例变量的值，于是，就需要使用实例变量的类型的默认值（对于引用类型的实例变量，
需要对应的类提供无参构造方法）

**使用ObjectInputStream和ObjectOutputStream 对可序列化对象进行序列化和反序列化操作**

	public class Person implements Serializable {
		private int age;
		private String name;
		
		public Persion(String name, int age){
			this.age = age;
			this.name = name;
		}

		//省略setter和getter方法
	}

	---------------------------------------
	//对Person进行序列化和反序列化操作
	try {
		Person person = new Person("zhangsan", 20);

		ObjectOutputStream oos = new ObjectOutputStream(
				new FileOutputStream("person.txt"));

		oos.writeObject(person);
		
	} catch (IOException e) {}
	-----------------------------------
	//对person.txt文件中的序列化对象进行反序列化操作
	try {

		ObjectInputStream ois = new ObjectInputStream(new FileInputStream("person.txt"));
	
		Person person1 = (Person) ois.readObject();

	} catch (IOException e) {
	
	} catch (ClassNotFoundException e) {}

如上，通过调用ObjectOutputStream.writeObject(Object obj)方法对一个可序列化的obj对象进行序列化操作
时，obj对象中的实例变量都会被序列化，如果obj对象中存在引用类型的实例变量，那么要保证此实例变量引用的
对象也是可序列化的，否则会报NotSerializableException异常。当然也可用transient关键字修饰这个实例
变量，此时，该实例变量就不会参与所在对象的序列化过程。

**可序列化对象中，对引用类型的实例变量进行序列化时，所产生的问题和解决方式**
考虑如下代码情形：

	public class Person implements Serializable {
		private int age;
		private String name;
		
		public Persion(String name, int age){
			this.age = age;
			this.name = name;
		}

		//省略setter和getter方法
	}

	public class Teacher implements Serializable {
		private String teacherName;
		private Person student;
		
		public Persion(String teacherName, Person student){
			this.student = student;
			this.teacherName = teacherName;
		}

		//省略setter和getter方法
	}

	--------------------------
	Person student = new Person("xiaoming", 20);

	Teacher chineseTeacher = new Teacher("lilaoshi", student);
	Teacher englishTeacher = new Teacher("zhanglaoshi", student);

	try {

		ObjectOutputStream oos = new ObjectOutputStream(
				new FileOutputStream("teacher.txt"));

		oos.writeObject(chineseTeacher);
		oos.writeObject(englishTeacher);
		oos.writeObject(student);

	} catch (IOException) {

	}
	----------------------------------
	try {
		ObjectInputStream oos = new ObjectInputStream(new FileInputStream("teacher.txt"));

		Teacher chineseTeacher1 = (Teacher) oos.readObject();
		Teacher englishTeacher1 = (Teacher) oos.readObject();
		Person student1 = (Person)oos.readObject();

		//chineseTeacher.getStudent()和englishTeacher.getStudent()，以及student1是同一个对象
		boolean bool = chineseTeacher.getStudent() == englishTeacher.getStudent(); //true
		boolean bool1 = chineseTeacher.getStudent() == student1; //true
	
		//但是student1和student不是同一个对象
		
	} catch (IOException e) {
	
	} catch (ClassNotFoundException e) {}

通过上面的代码，想说明这样一个问题：
对多个相同引用类型的实例变量进行序列化时，如果不管这些实例变量是不是引用了相同的对象，都对每个实例变量
引用的对象进行一次序列化操作，那么就会存在同一个对象被多次序列化的情况。于是在反序列化操作之后，本来都
是引用了同一个对象的多个实例变量，就会各自引用不同的对象了。

为了避免上述问题的出现，Java的序列化机制规定：
当程序对一个可序列化对象执行序列化操作时，会先检查该对象之前是否已被序列化过，如果该对象在本次虚拟机中
（即在同一个进程中）从未被序列化过，那么系统就会对该对象执行序列化操作，同时会在序列化过程中为该对象
设置一个序列化编号，这个序列化编号就是用来判断某个对象是否执行过序列化操作的；
如果该对象已经被序列化过了，那么在此对它执行序列化操作时，只是输出一个序列化编号而已，并不会再次对此对象
执行序列化操作。
于是对于之前引用了同一个对象的多个实例变量，在反序列化操作时，根据这个对象的序列化编号，这些实例变量所
引用的对象仍然还是同一个。但要注意的是，反序列之后的生成这个被引用的对象跟之前的那个被序列化的对象并不是
同一个。

对上面问题的解决方式，还存在一个隐患：
当程序对可序列化对象执行了一次序列化操作之后，我们又对该对象的实例变量进行了更新。此时想要通过对这个
对象再进行一次序列化操作，以便用这个更新后的实例变量的值覆盖掉之前被序列化输出的旧值的这种想法是不可能
实现了的，因为再次对该对象进行序列化操作时，只是输出这个对象的序列化编号而已。
所以当我们在对一个对象进行序列化操作之前，要保证序列化操作之后，不会再对该对象的实例变量的值进行更新了。

**自定义序列化机制 —— 实现跟transient关键字相同的效果**
我们知道，在可序列化的类中，对于不想参与序列化过程的实例变量，我们可以用transient关键字来修饰它。
类似地，Java提供了自定义序列化机制让我们只对类中需要参与序列化过程的实例变量进行序列化操作。
在自定义序列化机制中，需要在这个类中实现如下特殊签名的方法：

	//在调用ObjectOutputStream.writeObject(obj);方法对obj对象进行序列化操作时，系统内部会执行
		该对象的类中的writeObject(ObjectOutputStream out)方法来完成对实例变量的序列化操作。
	//通过重写writeObject(ObjectOutputStream)方法，可以自主决定哪些实例变量需要进行序列化，以及
		怎样对其进行序列化（是否需要加密等操作）。
	private void writeObject(java.io.ObjectOutputStream out)
		throws IOException
	
	//在调用Object obj' = ObjectOutputStream.readObject();方法对从磁盘文件或网络节点传递过来的
		序列化对象IO流进行反序列化操作时，系统内部会执行该序列化对象的类中的
		readObject(ObjectInputStream in)方法来恢复实例变量的值。
	//通过重写readObject(ObjectInputStream)方法，可以自主决定需要反序列化哪些实例变量，以及如何进行
		反序列化（是否需要解密等操作）。通常，readObject和writeObject方法要相互对应，即如果
		writeObject中对实例变量进行了处理，那么readObject中要对其进行相应的反处理。从而正确恢复对象
	private void readObject(java.io.ObjectInputStream in)
		throws IOException, ClassNotFoundException;
	
	private void readObjectNoData()
		throws ObjectStreamException;

writeObject(ObjectOutputStream)中对实例变量进行序列化操作的顺序，应该和
readObject(ObjectInputStream)中恢复实例变量的顺序一致，否则不能正常恢复对象

	---------------------------
	public class Person implements Serializable {
		private int age;
		private String name;
		
		public Persion(String name, int age){
			this.age = age;
			this.name = name;
		}

		//省略setter和getter方法
		...
		private void writeObject(java.io.ObjectOutputStream out)
			throws IOException {
			out.writeObject(new StringBuffer(name).reverse());
			out.writeInt(age);
		}

		private void readObject(java.io.ObjectInputStream in)
			throws IOException, ClassNotFoundException {
			this.name = ((StringBuffer)in.readObject()).reverse();
			this.age = in.readInt();
		}
	}

	------------------
	try {
		Person person = new Person("zhangsan", 20);

		ObjectOutputStream oos = new ObjectOutputStream(
				new FileOutputStream("person.txt"));

		//执行此方法，内部会调用到Person类的writeObject方法对person对象中的实例变量进行序列化
		oos.writeObject(person);
		
	} catch (IOException e) {}

	---------------------
	try {

		ObjectInputStream ois = new ObjectInputStream(new FileInputStream("person.txt"));
	
		//执行此方法，内部会调用Person类的readObject方法恢复person1对象中的实例变量
		Person person1 = (Person) ois.readObject();

	} catch (IOException e) {
	
	} catch (ClassNotFoundException e) {}
	
**自定义序列化机制中的writeReplace()方法的作用**

	ANY-ACCESS-MODIFIER Object writeReplace() throws ObjectStreamException;

在可序列化的类中重写writeReplace()方法，会将本来要对此类的对象所进行的序列化过程，转换为对
writeReplace方法的返回值对象的序列化过程，如果返回值对象的类中也重写了writeReplace方法，那么这种
序列化对象的转换会一直进行下去，直到返回值对象的类中没有重写writeReplace方法或重写的writeReplace
方法返回null。最后，此次序列化过程中的目标序列化对象，
就是对应类中没有重写writeReplace方法（或重写的writeReplace方法返回null）的对象。于是，再调用这个
对象的writeObject(ObjectOutputStream)进行序列化操作。

重写writeReplace方法时，它的访问修饰符是任意的。所以要注意writeReplace的返回值对象所在的类的父类
有没有重写writeReplace方法，并用protected或public修饰，如果是这样的话，那么这个返回值对象还不是
最终执行序列化的对象。

在类A中重写了writeReplace方法，返回了另一个对象后，被序列化的对象就改变了，所以当对类A的对象进行序列
化操作，再进行反序列化操作时，ObjectOutputStream.readObject()返回的就不是类A的对象了，而是最后
一次writeReplace方法所返回的对象。

**自定义序列化机制中的readResolve()方法的作用**
在可序列化的类中重写readResolve()方法，会将反序列化对象替换为readResolve方法的返回值对象。
即对此类的对象执行序列化操作，再对序列化后的产物（磁盘文件，网络节点中的IO流）进行反序列化操作时，
恢复的对象被替换成readResolve方法的返回值对象。

readResolve方法可用来恢复一个类变量（static修饰的静态成员变量），或常量（static和final修饰的
成员变量）。

	ANY-ACCESS-MODIFIER Object readResolve() throws ObjectStreamException;

重写readResolve方法时，它的访问修饰符是任意的。所以要注意当父类重写的readResolve方法用public或
protected修饰时，子类的反序列化对象，会被父类中readResolve的返回值对象代替。
所以重写readResolve时，尽量使用private修饰，或者重写了readResolve的类用final修饰，即不让此类被继承

	ANY-ACCESS-MODIFIER static final long serialVersionUID = 42L;

**可序列化的类的版本**
在进行对象的反序列化操作时，是需要提供一份该对象的class文件的。于是存在一个问题，当我们在进行了对象的
序列化操作之后，修改了此对象的class文件，那么在对该对象进行反序列化操作时，就找不到原来的class文件了。
考虑到这种情况，Java序列化机制为可序列化类提供了一个类变量serialVersionUID，这个类变量用于标识Java
类的序列化版本

	private static final long serialVersionUID = 512L;

只要修改前后的两个class文件的serialVersionUID的值相同的，那么序列化机制就会把这两个class文件当成
同一个序列化版本。
如果不显示指定serialVersionUID的值，那么该类变量的值将由JVM根据类的相关信息计算，而修改后的类的计算
结果可能就会跟修改之前的计算结果不同，从而造成对象的反序列化过程因为序列化版本不兼容而失败。

可以通过%JAVA_HOME%\bin目录下的serialver.exe工具获得一个类的serialVersionUID的值，命令如下：

	serialver 全路径类名

需要注意的是，如果类的修改确实会导致该类的反序列化失败，那么应该为该类的serialVersionUID重新指定值，
如修改了实例变量的引用类型会导致反序列化失败

### 使用 Parcelable 接口实现序列化 ####
使用Parcelable接口实现类的可序列化的用法如下：

	public class Book implements Parcelable {
	
	    public int bookId;
	    public String bookName;
	
	    public Book() {}
	
	    public Book(int bookId, String bookName) {
	        this.bookId = bookId;
	        this.bookName = bookName;
	    }
	
	    public int describeContents() {
	        return 0;
	    }
	
	    public void writeToParcel(Parcel out, int flags) {
	        out.writeInt(bookId);
	        out.writeString(bookName);
	    }
	
	    public static final Parcelable.Creator<Book> CREATOR 
			= new Parcelable.Creator<Book>() {

	        public Book createFromParcel(Parcel in) {
	            return new Book(in);
	        }
	
	        public Book[] newArray(int size) {
	            return new Book[size];
	        }
	    };
	
	    private Book(Parcel in) {
	        bookId = in.readInt();
	        bookName = in.readString();
	    }
	}

	------
	public class User implements Parcelable {
	
	    public int userId;
	    public String userName;
	    public boolean isMale;
	
	    public Book book;
	
	    public User() {}
	
	    public User(int userId, String userName, boolean isMale) {
	        this.userId = userId;
	        this.userName = userName;
	        this.isMale = isMale;
	    }
	
	    public int describeContents() {
	        return 0;
	    }
	
	    public void writeToParcel(Parcel out, int flags) {
	        out.writeInt(userId);
	        out.writeString(userName);
	        out.writeInt(isMale ? 1 : 0);
	        out.writeParcelable(book, 0);
	    }
	
	    public static final Parcelable.Creator<User> CREATOR 
			= new Parcelable.Creator<User>() {

	        public User createFromParcel(Parcel in) {
	            return new User(in);
	        }
	
	        public User[] newArray(int size) {
	            return new User[size];
	        }
	    };
	
	    private User(Parcel in) {
	        userId = in.readInt();
	        userName = in.readString();
	        isMale = in.readInt() == 1;
	        book = in.readParcelable(Thread.currentThread().getContextClassLoader());
	    }
	}

如上所示，实现了Parcelable接口的类就是可序列化的。不过，Parcelable接口不是一个标记接口，实现这个接口
需要重写如下几个方法：

	public @ContentsFlags int describeContents();
		//重写此方法，通常直接返回0即可。表示当前对象不存在文件描述符。
			只有当前对象中存在文件描述符时，才需要返回1。

	public void writeToParcel(Parcel dest, @WriteFlags int flags);
		//对对象进行序列化操作，调用Parcel的writeXxx方法序列化实例变量
			参数flags只有0|1两种值，通常设为0即可，只有当对象需要作为返回值返回，不能立即释放资源
			时，flags才设置为1
		//Parcel提供了大量的writeXxx方法，实现大多数类型的实例变量的序列化。

重写Parcelable接口的writeToParcel方法，只是实现了对象的序列化操作，如果要进行反序列化操作时，还需要
定义一个类变量CREATOR，这个类变量的类型是Parcelable.Creator<T>，泛型T就是被反序列化的对象的类型，
Parcelable.Creator<T>类型的类变量引用的类也需要重写如下方法实现反序列化操作：

	public T createFromParcel(Parcel source);
		//调用Parcel提供的readXxx方法，从序列化后的对象中恢复实例变量，返回一个反序列化的对象

	public T[] newArray(int size);
		//创建size长度的被序列化的类型的数组，即return new T[size]

Android系统提供了许多实现了Parcelable接口的类，它们都是可序列化的，如Intent、Bundle、Bitmap、List
、Map等。需要注意的是，List、Map类型的实例变量要想被序列化，那么List、Map集合中的元素类型也必须是可
序列化的。

### Serializable 和 Parcelable 的比较 ###
Serializable的开销较Parcelable大，因为Serializable的序列化和反序列化过程需要大量的IO操作。

通过将对象序列化到内存上时，采用Parcelable接口；将对象序列化到磁盘文件中或网络节点上时，建议使用
Serializable接口。

# 基于 Binder 的跨进程通信（IPC） ##
- 从API的角度看， Binder是Android中的一个Java类 android.os.Binder。Binder类实现了
android.os.IBinder接口
- 从IPC角度来说，Binder是Android的一种跨进程通信的方式
- 在Linux中，Binder还可以被理解为一种虚拟的物理设备，设备驱动是/dev/binder
- 从Android Framework角度来说，Binder是ServiceManager用于连接各种Manager（ActivityManager、
WindowManager等等）和各种Manager对应的ManagerService的桥梁；
- 从Android应用层来说，Binder是客户端和服务端之间进行通信的媒介。

基于Binder的跨进程通信，其实就是通过以Binder、IBinder、IInterface、Parcelable、Parcel这几个主要
的API为核心，重写API提供的相关方法，分别编写客户端进程的代码和服务端进程的代码。代码编写完成后，就可以
在两端进程中调用相关API方法，在两端之间传递实现了Parcelable接口的序列化后的对象数据。

而AIDL其实并不算是另外一种跨进程通信的方式，它只是一种实现基于Binder的跨进程通信的手段。
通过上面对Binder通信方式的介绍，可以知道手动编写Binder通信相关的代码是比较麻烦的，于是Android提供一种
方便的手段，辅助我们完成Binder通信相关代码的实现，这种方便的手段就是AIDL（Android Interface 
Definition Language，即Android接口定义语言）。

下面举例介绍 通过AIDL实现的基于Binder的跨进程通信


